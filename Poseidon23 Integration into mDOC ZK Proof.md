## Goal

Replace issuer ECDSA verification with a hiding commitment `C = Poseidon23(e1 || r)` that bridges longfellow ZK proof and an external zkdilithium STARK proof. Issuer authentication is delegated to the STARK side. Longfellow proves the commitment is well-formed.

---
## High-level wiring

```

╔════════════════════════════════════════════╗

║ PUBLIC INPUTS (verifier) ║

║ ║

║ pkX, pkY (issuer pk -- only ║

║ used outside ║

║ longfellow now) ║

║ attrs[], now (claim disclosure) ║

║ transcript hash e2 (device sig msg) ║

║ C = 12 x Fp<1> (Poseidon commit) ║

║ MAC_e1[32] (binds e1 bytes) ║

║ MAC_dpk[4] (binds dpkX, dpkY) ║

║ av (Fiat-Shamir chal.) ║

╚════════════════════════════════════════════╝

│ │ │

┌───────────┘ │ └───────────┐

▼ ▼ ▼

┌────────────────────────┐ ┌────────────────────────┐ ┌────────────────────┐

│ HASH CIRCUIT │ │ POSEIDON CIRCUIT │ │ SIG CIRCUIT │

│ field: GF(2^128) │ │ field: Fp2<Fp<1>> │ │ field: Fp256 │

│ │ │ │ │ │

│ Private witness: │ │ Private witness: │ │ Private witness: │

│ MSO bytes │ │ e1[0..31] (32 bytes) │ │ dpkX, dpkY │

│ SHA block witnesses │ │ r[0..K-1] (rand elts) │ │ device_sig (r,s) │

│ e1 (256 bits) │ │ sbox witnesses │ │ ECDSA verify trace │

│ dpkX, dpkY (extracted) │ │ ap_e1[32] │ │ ap_dpk[2] │

│ ap_e1[32], ap_dpk[2] │ │ │ │ │

│ │ │ │ │ │

│ Computation: │ │ Computation: │ │ Computation: │

│ 1. e1 = SHA256( │ │ 1. assemble preimage │ │ 1. ECDSA verify │

│ COSE1(MSO)) │ │ P = e1 || r │ │ device sig │

│ 2. validFrom <= now │ │ 2. C' = Poseidon23(P) │ │ over e2 │

│ <= validUntil │ │ 3. assert C' == C │ │ 2. MAC check │

│ 3. extract dpkX, dpkY │ │ 4. range check │ │ dpkX, dpkY │

│ 4. attribute commits │ │ e1[i] < 2^8 │ │ │

│ 5. split e1 into │ │ 5. MAC check │ │ │

│ 32 bytes │ │ e1 bytes │ │ │

│ 6. MAC check │ │ │ │ │

│ e1 bytes + dpk │ │ │ │ │

│ │ │ │ │ │

└─────────┬───────────────┘ └─────────┬───────────────┘ └─────────┬───────────┘

│ │ │

│ MAC_e1[i] = av*e1[i] + ap_e1[i] (same on both sides) │

└────────────────────────────┘ │

│ │

│ MAC_dpk[j] = av*dpk_j + ap_dpk_j (same on both sides) │

└─────────────────────────────────────────────────────────┘

  

Issuer ECDSA verification: REMOVED

External zkdilithium STARK proves Dilithium on e1

AND C = Poseidon23(e1 || r), sharing only C.

```
---
## Critical invariants

1. **Same `e1` across hash and Poseidon circuits**. Enforced by 32 GF(2^128) MACs over the 32 bytes of e1. Each byte is committed separately. Cross-circuit binding: prover commits `ap_e1[i]` before `av` is derived, so cannot swap byte values.

2. **`e1` and `r` stay private**. Only `C` is public. `r` provides hiding so the same MSO produces different `C` across sessions (unlinkability).

3. **Field bridge for MAC**:
	- Hash circuit native: $GF(2^{128})$. Direct.
	- Poseidon circuit native: $Fp2<Fp<1>>$. Each $Fp<1>$ element fits in 23 bits, so each byte (8 bits) is representable.
	- MAC arithmetic happens over $GF(2^{128})$ in both. The Poseidon circuit must implement $GF(2^{128})$ MAC checks as gates over $Fp2<Fp<1>>$.

4. **Fiat-Shamir order**: commit hash, commit Poseidon, commit sig, derive `av`, compute MACs, prove all three. Wrong order = soundness break.
---
## Encoding of `e1` and `r`

  

| Item    | Type  | Count | Constraint                                                              |
| ------- | ----- | ----- | ----------------------------------------------------------------------- |
| `e1[i]` | Fp<1> | 32    | `0 <= e1[i] < 2^8` (range check inside Poseidon circuit)                |
| `r[j]`  | Fp<1> | 8-12  | uniform random, total entropy >= 128 bits (8 elts * 23 bits = 184 bits) |

Preimage layout: `[e1[0], e1[1], ..., e1[31], r[0], r[1], ..., r[K-1]]`. Total = 32 + K elements.
Poseidon23 has `kRate = 24` per permutation. Preimage of 32+8 = 40 elements → 2 permutations. Digest = first 12 state elements = `C`.
Match this layout exactly on the zkdilithium STARK side.

---
## Implementation plan

### Step 1: Remove issuer ECDSA
**Files:** `mdoc_signature.h`, `mdoc_witness.h`, `mdoc_zk.cc`.

- `MdocSignature::Witness`: remove `e_`, `mdoc_sig_`.

- `MdocSignature::assert_signatures`: remove first `verify_signature3` call; remove `verify_mac` for `e`.

- `MdocSignatureWitness`: remove `e_`, `ew_`, `macs_[0]`.

- `MdocSignatureWitness::compute_witness`: remove issuer sig parsing.

- `mdoc_zk.cc::fill_signature_inputs`: drop `pkX`, `pkY`, `e` (or keep API-compat as unused).

- `mdoc_zk.cc::fill_witness`: `state.common` becomes `{dpkX, dpkY}` (2 vals not 3); reduce MAC count.

- `kSigMacIndex`, `getHashMacIndex`: recompute offsets.
### Step 2: Add e1 byte exposure to hash circuit

  

**Files:** `mdoc_hash.h`, `mdoc_witness.h`.

  

The SHA256 circuit already produces `e1` as a `v256` (256 bit wires). The 32 bytes are contiguous slices of those bits.

  

- In `MdocHash::assert_valid_hash_mdoc`: after `sha_.assert_message_hash` produces `e`, slice it into `v8 e1_bytes[32]`.

- For each byte, add a MAC constraint: `MAC_e1[i] == av * e1_bytes[i] + ap_e1[i]`.

- `MdocHash::Witness`: add `ap_e1[32]` private wire array.

- `MdocHash` public input: add `MAC_e1[32]` (32 GF(2^128) elements) + `av` already there.

  

`MdocHashWitness::fill_witness`: fill `ap_e1[32]` from `MacGF2Witness` samples.

  

Estimated effort: 2-3 days. The bit-slicing is mechanical; the MAC plumbing requires care.

  

### Step 3: Build MdocPoseidon circuit

  

**New files:** `mdoc_poseidon.h`, additions to `mdoc_witness.h`.

  

```cpp

// mdoc_poseidon.h

template <class LogicCircuit, class Field>

class MdocPoseidon {

// Field = Fp2<Fp<1>>

public:

struct Witness {

EltW e1_bytes[32]; // private, MAC-linked to hash circuit

EltW r[K]; // private, prover-sampled

EltW sbox_wits[...]; // private, Poseidon23 sbox witnesses

EltW ap_e1[32]; // private, MAC key shares

// (no ap for r -- r is fully private to Poseidon, not cross-circuit)

};

  

void assert_valid(

const EltW C[12], // public: claimed commitment

const EltW MAC_e1[32], // public: cross-circuit MAC

const EltW av, // public: Fiat-Shamir challenge

const Witness& vw) const {

// 1. range check: each e1_bytes[i] < 2^8

for (size_t i = 0; i < 32; ++i)

range_check_u8(vw.e1_bytes[i]);

  

// 2. assemble preimage

std::vector<EltW> preimage;

for (i in 0..32) preimage.push_back(vw.e1_bytes[i]);

for (j in 0..K) preimage.push_back(vw.r[j]);

  

// 3. run Poseidon23, assert output == C

Poseidon23Circuit<LogicCircuit> P(lc_, constants_);

P.assert_preimage(preimage, C);

  

// 4. MAC binding

for (size_t i = 0; i < 32; ++i) {

// MAC_e1[i] == av * e1_bytes[i] + ap_e1[i]

lc_.assert_eq(MAC_e1[i],

lc_.add(lc_.mul(av, vw.e1_bytes[i]), vw.ap_e1[i]));

}

}

};

```

  

**The tricky part**: `MAC_e1`, `av`, `ap_e1` are GF(2^128) values used by the hash circuit. The Poseidon circuit is over Fp2<Fp<1>>. See "Open question 1".

  

Estimated effort: 3-5 days for clean MAC bridging.

  

### Step 4: Build MdocPoseidonWitness

  

**Files:** `mdoc_witness.h`.

  

```cpp

template <class Field>

class MdocPoseidonWitness {

public:

std::array<BaseElt, 32> e1_bytes_;

std::array<BaseElt, K> r_;

std::vector<BaseElt> sbox_wits_;

std::array<BaseElt, 32> ap_e1_;

std::array<BaseElt, 12> C_; // computed digest

  

void compute_witness(const uint8_t e1[32], // from hash side

SecureRandomEngine& rng) {

// 1. fill e1_bytes from raw bytes

for (i in 0..32) e1_bytes_[i] = F.of_scalar(e1[i]);

  

// 2. sample r

for (j in 0..K) r_[j] = sample_random_fp1(rng);

  

// 3. assemble preimage

std::vector<BaseElt> preimage(e1_bytes_.begin(), e1_bytes_.end());

preimage.insert(preimage.end(), r_.begin(), r_.end());

  

// 4. compute digest via native_digest

auto digest = poseidon23::native_digest(preimage.data(),

preimage.size(), F, C);

for (i in 0..12) C_[i] = digest[i];

  

// 5. compute sbox witnesses

sbox_wits_ = poseidon23::sbox_witnesses_for_digest(...);

  

// 6. ap_e1 sampled by MAC layer separately

}

};

```

  

Estimated effort: 1 day.

  

### Step 5: Integrate in `mdoc_zk.cc`

  

**File:** `mdoc_zk.cc`.

  

- Parse 3 circuits from byte blob: `c_hash, c_pos, c_sig`.

- Create 3 ZkProofs, 3 ZkProvers, 3 ZkVerifiers.

- New RS factory for Fp2<Fp<1>>:

```cpp

using PoseidonField = Fp2<Fp<1>>;

PoseidonField F2_p1(base_field_p1);

ProofElt omega = F2_p1.of_string("2187"); // generator

FFTConvolutionFactory<PoseidonField> fft_p(F2_p1, omega, 1ull << 20);

ReedSolomonFactory<PoseidonField, ...> rsf_p(fft_p, F2_p1);

```

- Commit order:

```

hash_p.commit(h_zk, W_hash, tp, rng);

pos_p.commit(p_zk, W_pos, tp, rng);

sig_p.commit(s_zk, W_sig, tp, rng);

av = generate_mac_key(tp);

compute MACs, update_macs in all 3 dense arrays

hash_p.prove(...); pos_p.prove(...); sig_p.prove(...);

```

- Verifier mirrors this order exactly.

- Serialize: `[MAC_e1[32]][MAC_dpk[2]][hash_proof][pos_proof][sig_proof]`.

- API: add `out_C[12]` to prover return; add `in_C[12]` to verifier input.

- API: prover also returns `r` (so STARK side can use same r).

  

Estimated effort: 4-5 days.

  

### Step 6: Regenerate circuits, update zk_spec

  

- Update `mdoc_generate_circuit.cc` to emit all 3 circuits.

- Run `circuit_maker` to produce new compressed circuit bytes.

- Compute new circuit hashes; add new entry to `kZkSpecs` in `zk_spec.cc`.

- Update test `mdoc_zk_test.cc`.

  

Estimated effort: 2-3 days.

  

### Step 7: Cross-validation with zkdilithium

  

- Write a small Python or C++ harness that:

1. Computes `e1 = SHA256(COSE1(MSO))` from a sample mDOC.

2. Samples `r`.

3. Computes `C = Poseidon23(e1_bytes || r)` using **both** the longfellow native impl and the zkdilithium Rust impl.

4. Asserts they match bit-exactly.

- This catches encoding mismatches before they cost weeks.

  

Estimated effort: 1-2 days.

  

---

  

## Open questions to resolve before coding

  

### 1. MAC cross-field bridge (HARD)

  

How does the Poseidon circuit (over Fp2<Fp<1>>) verify a MAC equation `MAC = av * e1_byte + ap` that's defined over GF(2^128)?

  

Options:

  

**(a)** Implement GF(2^128) arithmetic as gates over Fp2<Fp<1>>. Multiplying two 128-bit polynomials in Fp<1> arithmetic = lots of constraints. Expensive but clean.

  

**(b)** Use a different MAC scheme for the hash-Poseidon link, native to Fp<1>. For example, MAC over Fp<1> directly. Hash circuit then needs to compute Fp<1> arithmetic over GF(2^128) — also expensive, mirror problem.

  

**(c)** Use Fp2<Fp<1>> for **everything** (re-implement SHA over Fp<1>). Eliminates the bridge but slows down SHA significantly.

  

**(d)** Replace MAC with a different commitment-equality scheme — e.g., compute `H(e1_bytes)` (where H is a small hash like Poseidon) in BOTH circuits and equate the hash outputs.

  

Decide before Step 3.

  

### 2. r size and randomness source

  

How many Fp<1> elements? Where does r come from? Likely answer: 8-12 elements sampled from `SecureRandomEngine` at proof time. Returned alongside the proof so the zkdilithium prover can reuse the same r.

  

### 3. Match zkdilithium's expected input encoding

  

Verify: does zkdilithium's Poseidon23 take e1 as 32 bytes-as-Fp<1>, or some other decomposition? Check `zkdilithium/src/utils/poseidon_23_spec.rs` and the signing routine.

  

---
## Test strategy

  

1. Native Poseidon23 produces same C as zkdilithium for sample (e1, r).

2. EvaluationBackend run of Poseidon circuit matches native.

3. End-to-end ZK proof roundtrip (Step 7 harness).

4. Existing mdoc_zk_test.cc samples still verify (with issuer ECDSA removed).

5. Negative test: tamper with one byte of e1 in Poseidon circuit -> proof rejects (MAC mismatch).

  

## Files touched

| File | Change |
|------|--------|
| `mdoc_signature.h` | remove issuer ECDSA |
| `mdoc_hash.h` | add e1 byte slicing + MAC check |
| `mdoc_poseidon.h` | **NEW** — Poseidon sub-circuit |
| `mdoc_witness.h` | shrink MdocSignatureWitness; add MdocPoseidonWitness |
| `mdoc_zk.cc` | 3-circuit prover/verifier orchestration |
| `mdoc_zk.h` | C API: add `out_C`, `in_C`, `r` |
| `mdoc_generate_circuit.cc` | emit 3 circuits |
| `zk_spec.cc` | new ZkSpecStruct entry |
| `mdoc_zk_test.cc` | update tests |