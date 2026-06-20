# PQAC Parallel Proving --- Pseudo-algorithm

Formal specification of the presentation-proof protocol: the two **proof systems**
`STARK` and `Ligero` (each a module bundling the relation it proves with its
prover/verifier), the **Orchestrator** that builds the instances/witnesses and
invokes the provers, and the **Verifier**. All semantics are in the relations and
procedure bodies (no meaning is carried by comments).

---

## 1. Notation

| Notation                                              | Meaning                                                                                                        |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| $x \mathbin{\Vert} y$                                 | concatenation                                                                                                  |
| $[\,\mathrm{cond}\,]$                                 | $1$ if cond holds, $0$ otherwise                                                                               |
| $z = z_{\mathrm{hi}} \mathbin{\Vert} z_{\mathrm{lo}}$ | for $z \in \{0,1\}^{256}$: $\;z_{\mathrm{hi}}, z_{\mathrm{lo}} \in \{0,1\}^{128}$ (hi = most-significant half) |

---

## 2. Glossary of symbols

What each name represents (the formal types are in §3, the roles below):

**Credential & session**

- $MSO$ --- the user's credential (mobile Security Object), a byte string laid out as an array of fields.
- $tr$ --- **session transcript**: a unique, fresh value jointly produced by prover and verifier during the authentication/handshake before the presentation. It binds the proof to one specific session (anti-replay / freshness) and is the nonce the device signs for device binding. Used in $k_v$ derivation and in $(L7)$.
- $now$ --- current Unix timestamp, checked against the credential's validity window $(L6)$.
- $attr$ --- the attribute value being disclosed.
- $id$ --- index of $attr$ inside $MSO$.

**Credential hash & commitments**

- $e_1$ --- $\mathrm{SHA256}$ of the credential; the message the issuer actually signed. Shared (privately) by both proofs.
- $R_S$ --- STARK-side commitment to $(k_p, e_1)$ (Poseidon23). Locks the prover's key share so $e_1$ cannot be swapped.
- $R_L$ --- Ligero-side commitment to $(k_p, e_1)$ (SHA256). Same purpose, on the other proof system.

**MAC (links the two proofs)**

- $k_p$ --- prover's secret key share, fresh per presentation (gives unlinkability).
- $k_v$ --- verifier's key share, derived as $\mathrm{SHA256}(R_L \mathbin{\Vert} R_S \mathbin{\Vert} tr)$. Depends on **both** commitments, so it cannot be fixed before committing to $e_1$.
- $a, b$ --- the two 128-bit halves of $k_v \oplus k_p$ (each an element of $\mathbb{F}$); the affine-MAC key ($a = $ high, $b = $ low).
- $m$ --- the **MAC tag** of $e_1$: $\;m = a \cdot e_1 + b \in \mathbb{F}$ (arithmetic in $\mathbb{F} = \mathrm{GF}(2^{256})$). It is an authentication tag, not a ciphertext --- it neither hides nor encrypts $e_1$. The **same** $m$ appears in both proofs and is the value the verifier cross-checks.

**Keys & signatures**

- $PK_{II}$ --- issuer public key (Dilithium / post-quantum).
- $sign$ --- issuer's Dilithium signature on $e_1$ $(S3)$.
- $pk_{dx}, pk_{dy}$ --- device public key (the two EC coordinates).
- $(r_2, s_2)$ --- device's ECDSA-P256 signature over the session nonce $(L7)$, proving device binding.

**Device-auth black boxes (from Frigo--Shelat, left unspecified)**

- $H$ --- the hash applied to $tr \mathbin{\Vert} hdr$ before the device ECDSA check.
- $hdr$ --- the DeviceAuth header prepended to $tr$.

**Outputs**

- $\pi_S, \pi_L$ --- the STARK and Ligero zero-knowledge proofs.
- $Pres$ --- the presentation $(X_S, X_L, \pi_S, \pi_L)$ handed to the verifier.

---

## 3. Types (instances, witnesses, proofs)

$$
\begin{aligned}

X_S \;=\;& (PK_{II},\; m,\; k_v,\; R_S) && \text{STARK instance (public)} \\
W_S \;=\;& (sign,\; e_1,\; k_p, && \text{STARK witness (private)} \\
        & \phantom{(}\, a,\; b) \\[6pt]
X_L \;=\;& (attr:\{0,1\}^{*},\; id:\mathrm{int},\; tr:\{0,1\}^{*},\; now:\mathrm{int}, && \text{Ligero instance (public)} \\
        & \phantom{(}\, m:\mathbb{F},\; k_v:\{0,1\}^{256},\; R_L:\{0,1\}^{256}) \\
W_L \;=\;& (r_2:\{0,1\}^{256},\; s_2:\{0,1\}^{256},\; MSO:\{0,1\}^{*}, && \text{Ligero witness (private)} \\
        & \phantom{(}\, pk_{dx}:\{0,1\}^{256},\; pk_{dy}:\{0,1\}^{256},\; k_p:\{0,1\}^{256},\; e_1:\{0,1\}^{256}, \\
        & \phantom{(}\, a:\{0,1\}^{128},\; b:\{0,1\}^{128}) \\[6pt]
\end{aligned}
$$

---

## 4. Assumed components (signatures known before reading the procedures)

$$
\begin{aligned}
\mathrm{SHA256}     &: \{0,1\}^{*} \to \{0,1\}^{256} \\
\mathrm{Poseidon23} &: \{0,1\}^{*} \to \{0,1\}^{256} \\
H                   &: \{0,1\}^{*} \to \{0,1\}^{256} && \text{unspecified hash (Frigo--Shelat device check)} \\
hdr                 &: \{0,1\}^{*} && \text{unspecified DeviceAuth header (Frigo--Shelat)} \\
\mathrm{rand\_bytes}&: (n:\mathrm{int}) \to \{0,1\}^{8n} && \text{LaTeX: } \mathtt{rand.randombytes(n)} \\
\mathrm{now\_ts}    &: () \to \mathrm{int} && \text{LaTeX: } \mathtt{time.getTimestamp(0)};\ \text{current Unix time} \\
\mathrm{attr\_at}   &: (MSO:\{0,1\}^{*},\, id:\mathrm{int}) \to \{0,1\}^{*} && \text{the } id\text{-th attribute element} \\[4pt]
\mathrm{Dilithium.Verify} &: (sig:\{0,1\}^{*},\, msg:\{0,1\}^{256},\, pk:\{0,1\}^{*}) \to \{0,1\} \\
\mathrm{p256.Verify} &: (sig:(\{0,1\}^{256},\{0,1\}^{256}),\, msg:\{0,1\}^{256},\, pk:(\{0,1\}^{256},\{0,1\}^{256})) \to \{0,1\} \\[4pt]
\mathrm{parallel}   &: (f:()\to A,\; g:()\to B) \to (A, B) && \text{run both thunks concurrently}
\end{aligned}
$$

---

## 5. Proof systems
### Module `STARK`


Relation $R(x, w)$, parsing $x = (PK_{II}, m, k_v, R_S)$ and $w = (sign, e_1, k_p, a, b)$:

$$
\begin{aligned}
R(x,w) \;=\;
        & \big[\, R_S = \mathrm{Poseidon23}(k_p \oplus e_1) \,\big]                 && \textbf{(S1)} \\
\wedge\;& \big[\, a = (k_v \oplus k_p)_{\mathrm{hi}} \,\big]                          && \textbf{(S2a)} \\
\wedge\;& \big[\, b = (k_v \oplus k_p)_{\mathrm{lo}} \,\big]                          && \textbf{(S2b)} \\
\wedge\;& \big[\, m = a \cdot e_1 + b \,\big]                                         && \textbf{(S2c)} \\
\wedge\;& \big[\, \mathrm{Dilithium.Verify}(sign, e_1, PK_{II}) = 1 \,\big]           && \textbf{(S3)}
\end{aligned}
$$

Prover / verifier contracts:

$$
\begin{aligned}
&\mathrm{Prove}(x{:}\,X_S,\, w{:}\,W_S) \to (\pi{:}\,\mathrm{Proof})
  && \textbf{require } R(x,w)=1;\quad \textbf{ensure } \mathrm{Verify}(x,\pi)=1 \\
&\mathrm{Verify}(x{:}\,X_S,\, \pi{:}\,\mathrm{Proof}) \to (b{:}\,\{0,1\})
  && \textbf{ensure } b=1 \Rightarrow \exists\, w'{:}\,W_S.\; R(x,w')=1
\end{aligned}
$$

- **(S1)** $R_S$ is the STARK commitment opening of $(k_p, e_1)$.
- **(S2a),(S2b)** the MAC key halves $a, b$ are **asserted** to equal the high/low halves of $k_v \oplus k_p$ (a wrong $a$ or $b$ makes the proof fail).
- **(S2c)** $m$ is the affine MAC of $e_1$ under that key: $m = a \cdot e_1 + b$.
- **(S3)** $sign$ is a valid issuer Dilithium signature on $e_1$.

### Module `Ligero`

Relation $R(x, w)$, parsing $x = (attr, id, tr, now, m, k_v, R_L)$ and $w = (r_2, s_2, MSO, pk_{dx}, pk_{dy}, k_p, e_1, a, b)$:

$$
\begin{aligned}
R(x,w) \;=\;
        & \big[\, e_1 = \mathrm{SHA256}(MSO[0{:}183]) \,\big]                          && \textbf{(L1)} \\
\wedge\;& \big[\, R_L = \mathrm{SHA256}(k_p \oplus e_1) \,\big]                         && \textbf{(L2)} \\
\wedge\;& \big[\, a = (k_v \oplus k_p)_{\mathrm{hi}} \,\big]                            && \textbf{(L3a)} \\
\wedge\;& \big[\, b = (k_v \oplus k_p)_{\mathrm{lo}} \,\big]                            && \textbf{(L3b)} \\
\wedge\;& \big[\, m = a \cdot e_1 + b \,\big]                                           && \textbf{(L3c)} \\
\wedge\;& \big[\, attr = \mathrm{attr\_at}(MSO, id) \,\big]                             && \textbf{(L4)} \\
\wedge\;& \big[\, MSO[128{:}160] = \mathrm{SHA256}(pk_{dx} \mathbin{\Vert} pk_{dy}) \,\big] && \textbf{(L5)} \\
\wedge\;& \big[\, \mathrm{int}(MSO[48{:}56]) < now \,\wedge\, now < \mathrm{int}(MSO[56{:}64]) \,\big] && \textbf{(L6)} \\
\wedge\;& \big[\, \mathrm{p256.Verify}((r_2, s_2),\, H(tr \mathbin{\Vert} hdr),\, (pk_{dx}, pk_{dy})) = 1 \,\big] && \textbf{(L7)}
\end{aligned}
$$

Prover / verifier contracts:

$$
\begin{aligned}
&\mathrm{Prove}(x{:}\,X_L,\, w{:}\,W_L) \to (\pi{:}\,\mathrm{Proof})
  && \textbf{require } R(x,w)=1;\quad \textbf{ensure } \mathrm{Verify}(x,\pi)=1 \\
&\mathrm{Verify}(x{:}\,X_L,\, \pi{:}\,\mathrm{Proof}) \to (b{:}\,\{0,1\})
  && \textbf{ensure } b=1 \Rightarrow \exists\, w'{:}\,W_L.\; R(x,w')=1
\end{aligned}
$$

- **(L1)** $e_1$ is the hash of the credential $MSO$.
- **(L2)** $R_L$ is the Ligero commitment opening of $(k_p, e_1)$.
- **(L3a),(L3b),(L3c)** same as $(S2a)$--$(S2c)$: $a, b$ are **asserted** to be the high/low halves of $k_v \oplus k_p$, and $m = a \cdot e_1 + b$ is the affine MAC of $e_1$.
- **(L4)** $attr$ is the disclosed attribute, the $id$-th attribute element of $MSO$.
- **(L5)** the device public key $(pk_{dx}, pk_{dy})$ hashes to the value stored in $MSO$.
- **(L6)** the current time $now$ lies within the credential's validity window in $MSO$.
- **(L7)** $(r_2, s_2)$ is a valid device ECDSA-P256 signature over the session nonce.

($\mathrm{STARK}.R$ and $\mathrm{Ligero}.R$ denote the two relation predicates when referenced from
outside their modules, e.g. in the Orchestrator's preconditions.)

---

## 6. Algorithm 1 --- Orchestrator

**Inputs:** $MSO:\{0,1\}^{*}$, $tr:\{0,1\}^{*}$, device signature $(r_2, s_2):(\{0,1\}^{256},\{0,1\}^{256})$, issuer signature $sign:\{0,1\}^{*}$, device public key $(pk_{dx}, pk_{dy}):(\{0,1\}^{256},\{0,1\}^{256})$, attribute $attr:\{0,1\}^{*}$ with index $id:\mathrm{int}$, issuer public key $PK_{II}:\{0,1\}^{*}$. **Output:** $Pres$.

$$
\begin{aligned}
&\mathbf{procedure}\ \mathrm{Orchestrator}(\dots) \to Pres \\
&\quad e_1 \leftarrow \mathrm{SHA256}(MSO[0{:}183]) \\
&\quad k_p \leftarrow \mathrm{rand\_bytes}(32) \\[4pt]
&\quad R_S \leftarrow \mathrm{Poseidon23}(k_p \oplus e_1) \\
&\quad R_L \leftarrow \mathrm{SHA256}(k_p \oplus e_1) \\
&\quad k_v \leftarrow \mathrm{SHA256}(R_L \mathbin{\Vert} R_S \mathbin{\Vert} tr) \\[4pt]
&\quad a \leftarrow (k_v \oplus k_p)_{\mathrm{hi}} \\
&\quad b \leftarrow (k_v \oplus k_p)_{\mathrm{lo}} \\
&\quad m \leftarrow a \cdot e_1 + b \\[4pt]
&\quad now \leftarrow \mathrm{now\_ts}() \\[4pt]
&\quad X_S \leftarrow (PK_{II}, m, k_v, R_S) \\
&\quad W_S \leftarrow (sign, e_1, k_p, a, b) \\
&\quad X_L \leftarrow (attr, id, tr, now, m, k_v, R_L) \\
&\quad W_L \leftarrow (r_2, s_2, MSO, pk_{dx}, pk_{dy}, k_p, e_1, a, b) \\[4pt]
&\quad \triangleright\ \text{PRE established above: } \mathrm{STARK}.R(X_S, W_S)=1 \text{ and } \mathrm{Ligero}.R(X_L, W_L)=1 \\
&\quad (\pi_S, \pi_L) \leftarrow \mathrm{parallel}\big(\; () \mapsto \mathrm{STARK.Prove}(X_S, W_S),\ \ () \mapsto \mathrm{Ligero.Prove}(X_L, W_L) \;\big) \\[4pt]
&\quad \mathbf{return}\ (X_S, X_L, \pi_S, \pi_L)
\end{aligned}
$$

---

## 7. Algorithm 4 --- Verifier

$$
\begin{aligned}
&\mathbf{procedure}\ \mathrm{Verifier}(pres{:}\,Pres,\, tr{:}\,\{0,1\}^{*}) \to \{\mathsf{accept}, \mathsf{reject}\} \\[4pt]
&\quad \mathbf{parse}\ pres = (X_S, X_L, \pi_S, \pi_L) \\
&\quad \mathbf{parse}\ X_S = (PK_{II}, m_S, k_{v,S}, R_S) \\
&\quad \mathbf{parse}\ X_L = (attr, id, tr_L, now, m_L, k_{v,L}, R_L) \\[4pt]
&\quad \triangleright\ \text{the two proofs must share the same MAC, the same verifier key, and this session} \\
&\quad \mathbf{if}\ m_S \neq m_L \;:\ \mathbf{return}\ \mathsf{reject} \\
&\quad \mathbf{if}\ k_{v,S} \neq k_{v,L} \;:\ \mathbf{return}\ \mathsf{reject} \\
&\quad \mathbf{if}\ tr_L \neq tr \;:\ \mathbf{return}\ \mathsf{reject} \\[4pt]
&\quad \triangleright\ \text{the verifier key must be the Fiat--Shamir hash of BOTH commitments + transcript} \\
&\quad \mathbf{if}\ k_{v,S} \neq \mathrm{SHA256}(R_L \mathbin{\Vert} R_S \mathbin{\Vert} tr) \;:\ \mathbf{return}\ \mathsf{reject} \\[4pt]
&\quad \triangleright\ \text{both proofs must verify against their public inputs} \\
&\quad \mathbf{if}\ \mathrm{STARK.Verify}(X_S, \pi_S) \neq 1 \;:\ \mathbf{return}\ \mathsf{reject} \\
&\quad \mathbf{if}\ \mathrm{Ligero.Verify}(X_L, \pi_L) \neq 1 \;:\ \mathbf{return}\ \mathsf{reject} \\[4pt]
&\quad \mathbf{return}\ \mathsf{accept}
\end{aligned}
$$
