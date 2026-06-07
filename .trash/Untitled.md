# ACTUAL IMPLEMENTATION

What the code on `parallel-ligero-stark-circuit-proof` actually runs today,
mirrored against `PLAN.md`.  Differences from the plan are flagged inline
with **(≠ PLAN)** and summarised at the end.

Sources inspected:
- `orchestrator/src/main.rs`, `orchestrator/src/verifier.rs`, `orchestrator/src/ligero_ffi.rs`
- `zkdilithium/src/starkpf/mod.rs`, `zkdilithium/src/starkpf/air.rs`, `zkdilithium/src/main.rs`
- `longfellow-zk/lib/circuits/mdoc/pqac_bridge.{cc,h}`, `longfellow-zk/lib/circuits/mdoc/mdoc_zk.cc`

Last verified runs: S24 (warm cache 1775 ms), A53 (warm cache 3079 ms).

---

## PSEUDOCODE

### ORCHESTRATOR (MSO, session_nonce)
*(`orchestrator/src/main.rs:67-215`)*

```
// e1 from test MSO (loaded inside the Ligero bridge, not by orchestrator)
// (≠ PLAN: PLAN says orchestrator computes e1 = sha256(MSO);
//  here the bridge hardcodes mdoc_tests[0] and returns e1 via pqac_get_test_e1)
e1 = pqac_get_test_e1()                                      // [pqac_bridge.cc:74-108]

// k_p hardcoded in main (not random)
// (≠ PLAN: PLAN says k_p = rand.randombytes(32))
k_p = [0xdeadbeef…, 0x0fedcba9…]                              // [main.rs:77-80]
k_p_bytes = k_p_to_bytes(k_p)                                 // 32 bytes LE

// Ligero commitment — plain SHA-256, no domain separator
R_L = SHA256(k_p_bytes || e1)                                 // [main.rs:55-61]

// STARK commitment — Poseidon over f23201
// Pack 512 bits (256 k_p ‖ 256 e1) into 24 rate elements (22 bits each),
// capacity = 0, apply 7 Poseidon rounds, take 12-element digest.
R_S = Poseidon_23(pack(k_p, e1))                              // [mod.rs:964-978]

// k_v derivation — uses session_nonce, not tr
// (≠ PLAN: PLAN says k_v = SHA256(R_L || R_S || tr).
//  Implementation: tr is replaced by a fixed session_nonce = [0x42; 32].)
k_v_bytes = SHA256(R_L || R_S_as_u32_LE_bytes(48 bytes) || session_nonce)
k_v = (u128::from_le(k_v_bytes[0..16]), u128::from_le(k_v_bytes[16..32]))
                                                              // [mod.rs:983-1004]

// MAC — single-term, NOT affine
// (≠ PLAN pseudocode, MATCHES PLAN PRIMITIVES note: "Frigo Shelat … MAC m = a*x")
e1_h = (le128(e1[0..16]), le128(e1[16..32]))
m_e  = ( gf2_128_mul(k_v[0] ^ k_p[0], e1_h[0]),
         gf2_128_mul(k_v[1] ^ k_p[1], e1_h[1]) )              // [mod.rs:929-935]

// av = STARK ↔ Ligero bridge value (Ligero's MAC key share)
av = k_v[0].to_le_bytes()  // 16 bytes                        // [main.rs:97]

// Parallel proof generation (real OS threads, not detached)
thread A (STARK):
    // witness now from test_fixture.rs::test_witness() — REAL Sign(sk, e1[:24])
    // output against the real issuer key (HTR/PUBT/PUBA in mod.rs == ISSUER/keys.txt).
    // m = bytesToFes(e1[:24]); com_r = [0; 12] (so STARK mu matches Sign's mu).
    proof_S = STARK.prove(witness = (z, w, qw, ctilde, m, com_r, e1, k_p),
                          public = (R_L, m_e, k_v, R_S))      // [mod.rs:1011-1047]
thread B (Ligero):
    (hash_root, sig_root, handle) = pqac_ligero_commit(k_p, R_L)
    proof_L = pqac_ligero_finalize(handle, av)                // [pqac_bridge.cc:149-186]

// Join + bundle
pqac_proof = { proof_S, proof_L, claims = (session_nonce, R_L, R_S, k_v, m_e) }
verify_pqac(pqac_proof)                                       // [verifier.rs:83-122]
```

---

### STARK PROOF  (`zkdilithium`, Winterfell over f23201 = 2^23 − 2^20 + 1)
*(`zkdilithium/src/starkpf/mod.rs`, `air.rs`)*

```
Public  = (PUBA, PUBT, HTR,            // Issuer Dilithium pk — HARDCODED constants
                                       //   (match ISSUER/keys.txt; HTR == h_tr from spec)
           R_L,                        // 32 bytes (passed by orchestrator)
           m_e,                        // (u128, u128)
           k_v,                        // (u128, u128)
           R_S)                        // [BaseElement; 12]
                                       // NOTE: m is NOT in PublicInputs — it's a private
                                       // witness so two presentations are unlinkable
                                       // (air.rs PublicInputs struct, lines 63-69).
Witness = (z, w, qw, ctilde,           // REAL Dilithium signature for msg = e1[0..24]
           m = bytesToFes(e1[0..24]),  //   (test_fixture.rs; signed by spec/zkdilithium.py:Sign)
           com_r = [0; 12],            //   (zero — works because 24-byte msg → msgFes_12,
                                       //    so STARK absorbs [m, 0×12] same as Sign absorbs m)
           e1, k_p)                    // bit-decomposed into trace columns

Trace: PADDED_TRACE_LENGTH = 512 rows, TRACE_WIDTH ≈ 6845 columns,
       AUX_WIDTH = 14 (RAP randomized columns)

Constraints actually evaluated:                              [air.rs:228…]

  // --- Dilithium signature validity (relations from zkDilithium ePrint:2023/414)
  1. BallSample correctness:
     ctilde → Poseidon → swap/sign permutation of c via SWAPASSERT, NEGASSERT,
     SWAPDECASSERT.                                            (air.rs)
  2. Message commitment mu = H(HTR ‖ m ‖ com_r) at rows COM_START..COM_END.
  3. w₁ hashing + binding: H(mu ‖ w₁) = ctilde at PIT_END-1 (CTILDEASSERT).
  4. w decomposition: w = w_low + 2γ₂·w_high − γ₂·w_bit, ranges enforced.
  5. z range: |z| ≤ γ₁ − β via 18-bit decomposition (ZASSERT).
  6. Polynomial identity at random γ:
        QW(γ)(γ²⁵⁶+1) + W(γ) = A(γ)·z(γ) − c(γ)·t(γ)         (evaluate_aux_transition)
     This is "Dilithium-verify-in-the-exponent".

  // --- MAC bridge gadget (this thesis)
  7. MAC bit columns: 256 e1 bits + 256 k_p bits + bitness asserts.
                                                            [air.rs:512-520]
  8. KEY = k_v ⊕ k_p: 256 + 256 XOR cells reconstructed from MAC bit cells,
     KEY committed to claimed k_v (boundary).               [mod.rs:6a314ff]
  9. Carry-less multiplication (k_v⊕k_p) · e1 over GF(2^128):
     INT parity columns + RED reduction columns; final RED_BIT bound to m_e
     at boundary.                                           [air.rs:567-625]
 10. Poseidon commitment opening (R_S):
     a. 12 capacity cells = 0 at COM_STARK_START.
     b. 24 rate cells = pack_bits_elem(k_p, e1, i) at COM_STARK_START
        (COM_PACK_ASSERT links rate to MAC bit cells, mod.rs:939-958).
     c. 7 Poseidon rounds enforced at COM_STARK_START..COM_STARK_END.
     d. 12 output cells = R_S at COM_STARK_END − 1.         [air.rs:638-867]

  // --- Partially enforced (was the main open gap; now narrowed)
 (~) "Dilithium.verify(sign, e1, PK_II)" as in PLAN: the STARK now proves a
      Dilithium signature over `m = bytesToFes(e1[0..24])` (first 24 bytes of
      the real mDOC e1 = 0x15c75330…) under the real issuer key whose
      `PUBT`/`PUBA`/`HTR` constants live in `mod.rs`.  Signature data
      `(z, w, qw, ctilde)` comes from `spec/zkdilithium.py:Sign(sk, e1[:24])`
      — see `zkdilithium/src/test_fixture.rs` + `ISSUER/{keys,signature_e1_24,e1}.txt`.

      Remaining gap: STARK absorbs `m ‖ com_r = 24` field elements at
      COM_START, so messages must satisfy `len(unpackFesLoose(msg)) ≤ 12`,
      i.e. `len(msg) ≤ 24` bytes.  Full 32-byte e1 (which gives `msgFes_16`)
      doesn't fit cleanly: padding `com_r = msgFes[12..16] ‖ 0×8` makes
      `mu_STARK == mu_Sign` algebraically (verified with Python Poseidon over
      f23201) but the prover still fails verification with "constraint
      evaluations over the out-of-domain frame are inconsistent" — root cause
      not yet isolated, likely in the COM→PIT permutation accounting when
      `com_r ≠ 0`.  Until that's fixed the STARK proves a sig on `e1[:24]`,
      not the full 32-byte e1.  See memory: `project-stark-msg-size`.
```

ProofOptions: 48 queries · blowup 4 · grinding 20 · Blake3-256 · sextic
extension · FRI folding 8 · max remainder 128.                [mod.rs:1022-1027]

Proof size: ~1255 KB.

---

### LIGERO PROOF  (`longfellow-zk` mDOC circuit + thin PQAC bridge)
*(`longfellow-zk/lib/circuits/mdoc/mdoc_zk.cc`, `pqac_bridge.cc`)*

The circuit is the unmodified production longfellow mDOC circuit (hash side
over GF(2^128), sig side over Fp256) with one addition: the **R_L
public-input gadget** (constraint 2 below).

```
Public  = (attrs, tr, now, doc_type, issuer_pk = (pkX, pkY),
           macs[6], av,                // 6×16-byte GF(2^128) MAC of (e, dpkX, dpkY) + av
           R_L)                        // 32 bytes, fed in LE-reversed (assert_hash v256)
Witness = (mdoc bytes, r_2, s_2, k_p)  // k_p stored in hw->rl_kp_ before fill

Constraints (from MdocHashWitness + MdocSignatureWitness via assert_hash and
ECDSA-Verify3 sub-circuits, mdoc_witness.h):

  1. e1 = SHA256(COSE1(tagged_mso_bytes))                    (Algorithm 10, rel. 1)
  2. R_L = SHA256(k_p ‖ e1) opened in-circuit (assert_hash v256 over k_p_bits||e1_bits;
     R_L public input is byte-reversed BE→LE so it matches the v256 layout —
     see memory:feedback-rl-byte-order).                      [mdoc_zk.cc:210-213]
  3. For each requested attribute i:
        h2_i = SHA256(nonce_i ‖ elementIdentifier_i ‖ elementValue_i)
        h2_i == MSO[valueDigests][namespace_i][digestID_i]    (rel. 2+3)
  4. Device-key binding by BYTE EQUALITY, not hash:
     - Witness offset vw.dev_key_info_.k locates deviceKeyInfo inside MSO.
     - cmp_buf shifted to expose bytes; CBOR prefix asserted; then
         assert_key(dpkx, &cmp_buf[kPkxInd])
         assert_key(dpky, &cmp_buf[kPkyInd])
       directly equate (dpkx, dpky) with the raw 32-byte EC coordinates
       sitting at fixed offsets inside the MSO's COSE_Key map.
                                                             [mdoc_hash.h:218-227]
     (≠ PLAN: PLAN red lines say "keys = MSO[96:160]; keys = SHA256(pkdx,pkdy)".
      The mDOC spec stores the device key in cleartext in deviceKeyInfo
      (COSE_Key fields -2 and -3 = EC point x/y), so there is no hash to
      check — equality is the right relation. PLAN is mistaken here.)
     (pkdx, pkdy) then flow to the sig circuit via the MAC bridge.  (rel. 4)
  5. Temporal validity: tstart < now < tend with
     tstart = MSO[validityInfo][validFrom], tend = …[validUntil].
                                                              (rel. 5+6+7)
  6. Issuer ECDSA-P256: Verify(issuer_pk, e1, (r1, s1)) == true.
                                                              (rel. 8, sig circuit)
  7. Device ECDSA-P256:
        e2 = SHA256(COSE1_Sign1("Signature1", DeviceAuth(tr, doc_type)))
        Verify((pkdx, pkdy), e2, (r2, s2)) == true.           (rel. 9, sig circuit)
  8. GF(2^128) MAC link: 6 hash-side macs of (e, dpkx, dpky) and 4 sig-side
     macs of (dpkx, dpky) share the same av; binds the two sub-circuits.
     mdoc_zk.cc:126-142 (compute_macs), 337-353 (generate_mac_key).

  9. PQAC MAC gadget (this thesis): m_e == (k_v ⊕ k_p) · e1 over GF(2^128),
     asserted in-circuit on BOTH halves.  k_v, m_e are new hash-circuit public
     inputs (two LE 128-bit halves each, filled right after R_L).  e1 halves
     come from the same `e` v256 the R_L gadget opens (low = e_bytes[0..15],
     high = e_bytes[16..31]); k_p halves from rl_kp.  The GF multiply uses
     logic.h `gf2_128_mul` — the exact gadget the STARK's `mac_taps.rs`
     reduction table was derived from — so the Ligero m_e is bit-identical to
     the STARK m_e for the same (k_v, k_p, e1).  Verified: the orchestrator's
     Rust `compute_mac` and the circuit agree (proof passes), and flipping one
     m_e bit makes the Ligero prover reject ("eval_circuit failed").
     [mdoc_generate_circuit.cc, mdoc_zk.cc fill_witness/fill_public_inputs]

Note: there are now TWO independent e1 bindings on the Ligero side.  (a) the
longfellow inner-mac macs[0..1] keyed by av = k_v[0] (legacy, retained), and
(b) the explicit m_e gadget above.  The cross-system link to the STARK is the
shared m_e (and r_l), not only av.                            (PLAN m_e: ✓)
```

Proof size: ~335 KB.  Circuit-gen ~14 s (S24) / ~27 s (A53) — cached to
`$PQAC_CACHE_DIR` / `$TMPDIR/pqac_circuit_cache` / `/data/local/tmp/pqac_circuit_cache`
on Android / `/tmp/pqac_circuit_cache` elsewhere.            [pqac_bridge.cc:28-50]

---

### JOINT VERIFIER
*(`orchestrator/src/verifier.rs:83-122`)*

```
verify_pqac(stark_proof, ligero_proof, claims):
  1. k_v' = derive_kv(session_nonce, R_L, R_S);  assert k_v' == claims.k_v
  2. STARK.verify(stark_proof, public = (R_L, m_e, k_v, R_S))
  3. av = k_v[0].to_le_bytes()
     pqac_ligero_verify(ligero_proof, av, R_L, k_v, m_e)
  4. Same R_L, k_v and m_e fed to both → cross-circuit binding.
  (✓) Explicit m_e check now enforced on BOTH sides: STARK over f23201 and
       Ligero via the in-circuit gf2_128_mul gadget (same GF poly).  k_v and
       m_e are passed to mdoc_verifier_set_kv_me before mdoc_verifier_check.
```

Cost on device: ~540 ms (S24) / ~960 ms (A53).

---

## PRIMITIVES  (vs PLAN.md § PRIMITIVES)

| PLAN proposal                                    | Implementation                                                  | Match? |
|--------------------------------------------------|-----------------------------------------------------------------|--------|
| STARK commitment = Poseidon                      | `Poseidon_23(pack(k_p, e1))`, 7 rounds, in-circuit              | ✓     |
| Ligero commitment = `sha256(k_p, e1)` (not merkle) | `R_L = SHA256(k_p ‖ e1)`, opened by in-circuit SHA-256          | ✓     |
| MAC = `a·x` (Frigo-Shelat, drop `+b`)            | `m_e = (k_v ⊕ k_p) · e1` over GF(2^128) — no `+b`               | ✓     |
| k_v = `SHA256(R_L ‖ R_S ‖ tr)`                   | `SHA256(R_L ‖ R_S_LE ‖ session_nonce)` — tr replaced by nonce   | ~     |

---

## GAPS vs PLAN.md  (where the demo is not yet a proof of the full claim)

1. **STARK binds e1 to the Dilithium message — but only the first 24 bytes.**
   The STARK now proves `Dilithium.verify((z,w,qw,ctilde), m, PUBT, PUBA)`
   where `m = bytesToFes(e1[0..24])`, plus the MAC `m_e = (k_v⊕k_p)·e1`
   over the full 32-byte e1.  Signature data and `(PUBT, PUBA, HTR)` match
   the real issuer keypair in `ISSUER/keys.txt` (sig in
   `ISSUER/signature_e1_24.txt`).  PLAN relation
   `true = Dilithium.verify(sign, e1, PK_II)` is therefore enforced for
   `e1[:24]`.
   **Residual gap:** the STARK absorbs `m ‖ com_r = 24` field elements at
   COM_START, so messages must satisfy `len(msg) ≤ 24` bytes.  Full 32-byte
   e1 needs either an AIR extension (wider rate / second absorption window)
   or untangling of the COM→PIT permutation accounting for `com_r ≠ 0`
   (where `mu_STARK == mu_Sign` algebraically but the prover still rejects).
   See memory: `project-stark-msg-size`.

2. **`e1` and the MSO come from `mdoc_tests[0]`** (hardcoded test
   credential inside the Ligero bridge).  The orchestrator has no MSO
   input; `pqac_get_test_e1()` returns the test vector's e1.  Production
   orchestrator must compute e1 from a real MSO and pass it to both sides.

3. **~~Ligero public inputs do not include `m_e` explicitly.~~ CLOSED.**
   The Ligero hash circuit now takes `k_v` and `m_e` as public inputs and
   asserts `m_e == (k_v⊕k_p)·e1` over GF(2^128) in-circuit, using the same
   `gf2_128_mul` gadget the STARK reduction table was derived from.  So the
   PLAN relation `m = mac_{k_v,k_p}(e1)` is now enforced on the Ligero side
   too, and cross-system binding rests on shared `R_L`, `k_v` and `m_e` (not
   only `av`).  Verified: orchestrator joint verify PASSes; tampering m_e by
   one bit makes the Ligero prover reject.

4. **`tr` (session transcript) is faked.**  Orchestrator uses
   `session_nonce = [0x42; 32]` and the Ligero side uses the test
   credential's `transcript` field for device-binding.  No real session
   wiring yet.

5. **PLAN's red device-key-hash relation is fictional.**  PLAN.md asks for
   `keys = MSO[96:160]; keys = SHA256(pkdx, pkdy)`, but mDOC doesn't store
   a hash — the device public key sits in cleartext at fixed CBOR offsets
   inside `deviceKeyInfo.deviceKey` (COSE_Key fields `-2`/`-3`).  The
   circuit therefore does the correct check (byte-equality between witness
   `(dpkx,dpky)` and the MSO bytes); PLAN should be revised to drop the
   SHA256, not the implementation extended.

---

## MEASURED RUNTIMES (warm cache, parallel STARK+Ligero)

| Stage              | Galaxy S24 (smartphone1) | Galaxy A53 (smartphone2) |
|--------------------|--------------------------|--------------------------|
| pre-compute        | 1.4 ms                   | 1.8 ms                   |
| STARK prove        | 1774 ms                  | 3078 ms                  |
| Ligero commit      | 296 ms                   | 481 ms                   |
| Ligero finalize    | 488 ms                   | 1055 ms                  |
| **wall-clock prove** | **1775 ms**            | **3079 ms**              |
| joint verify       | 540 ms                   | 958 ms                   |
| STARK proof size   | 1254.6 KB                | 1254.6 KB                |
| Ligero proof size  | 334.5 KB                 | 334.5 KB                 |

Cold-cache adds ~14 s (S24) / ~27 s (A53) for Ligero circuit generation.
Once cached to disk the cost is paid once per build.

---

## BUILD / RUN

```bash
# Linux release build
cargo build --release --manifest-path orchestrator/Cargo.toml
./orchestrator/target/release/pqac_orchestrator

# Android arm64 (NDK r28 in default location)
cargo build --release --target aarch64-linux-android --manifest-path orchestrator/Cargo.toml
adb push orchestrator/target/aarch64-linux-android/release/pqac_orchestrator /data/local/tmp/
adb push <NDK>/.../sysroot/usr/lib/aarch64-linux-android/libc++_shared.so /data/local/tmp/
adb shell 'cd /data/local/tmp && LD_LIBRARY_PATH=/data/local/tmp RUST_LOG=info ./pqac_orchestrator'
```

See `memory/project_android_run.md` for the libc.a-vs-libc.so link-order
pitfall that bit us once.
