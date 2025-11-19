# **Lesson 7A – Error Control (Deep Dive), Hamming Code, CRC**

## 1. Error Control Recap

Error control has two branches:

- **Error Detection** → detects *if* an error occurred.
- **Error Correction** → detects *where* the error is so it can be fixed.

Methods we cover: **simple parity**, **2‑D (block) parity**, **Hamming codes**, **CRC**.

---

## 2. Simple Parity (Quick, limited)

### Even / Odd Parity

- **Even parity:** choose parity bit so total number of 1s (data + parity) is even.
- **Odd parity:** choose parity bit so total number of 1s is odd.

**Properties**

- Uses one extra bit per block of data.
- Detects any *odd* number of bit errors (1, 3, 5, ...).
- **Cannot detect** errors that flip an even number of bits (2, 4, ...).
- **Cannot correct** — only signals "something is wrong."

**Examples**

- Data: `1010110` (ones = 4)

  - Even parity bit = `0` → Transmitted: `1010110 0`.
  - Odd parity bit  = `1` → Transmitted: `1010110 1`.

- If receiver gets `1010111 0` and computes parity it will notice mismatch → error detected.

---

## 3. Block Parity (2‑D Parity)

Block parity organizes bits into a 2‑D grid (rows × columns). We compute a parity bit for each row, each column, and a final overall parity.

### Structure (your 32‑bit example)

- Divide 32 bits into **4 rows × 8 columns**.
- Row parity bits: `P0, P1, P2, P3` (one at the right end of each row).
- Column parity bits: `P-0 ... P-7` (one beneath each column).
- Big `P` (bottom‑right) = XOR of all row parities XOR all column parities.

```
 b00 b01 b02 b03 b04 b05 b06 b07 | P0
 b10 b11 b12 b13 b14 b15 b16 b17 | P1
 b20 b21 b22 b23 b24 b25 b26 b27 | P2
 b30 b31 b32 b33 b34 b35 b36 b37 | P3
--------------------------------------
 P-0 P-1 P-2 P-3 P-4 P-5 P-6 P-7 |  P
```

### How it detects and corrects

- If a **single bit** flips (one error) → exactly one row parity and one column parity will change. Their intersection locates the wrong bit, and we can flip it back (correct it).
- If **multiple bits** flip, block parity usually *detects* them (nonzero row/column parities) but cannot always correct them.
- **Exception (undetected pattern):** A perfect 2×2 square (four bits arranged as a square) flips two bits in two rows and two bits in two columns — each affected row/column parity flips twice (cancels out) → **no change** in parities → **undetected**.

### Concrete numeric example (worked)

Take the 4 rows below (each row has 8 bits):

```
Row0: 1 1 0 0 1 0 1 0  # 11001010
Row1: 0 1 1 0 1 1 0 0  # 01101100
Row2: 1 0 1 1 0 0 1 1  # 10110011
Row3: 0 0 0 1 1 1 0 1  # 00011101
```

Compute row parities (XOR across each row):

- P0 = XOR(Row0) = 0
- P1 = XOR(Row1) = 0
- P2 = XOR(Row2) = 1
- P3 = XOR(Row3) = 0

Compute column parities (P-0 ... P-7) by XOR down each column:

- P-0 = 0, P-1 = 0, P-2 = 0, P-3 = 0, P-4 = 1, P-5 = 0, P-6 = 0, P-7 = 0

Big P = XOR(all row parities XOR all column parities) = 0

So the parity bits appended look like this (right/bottom):

```
... | 0
... | 0
... | 1
... | 0
----
0 0 0 0 1 0 0 0 | 0
```

#### Single‑bit error demonstration

Flip 1 bit: Row0, Column2 (row0 col2, 0‑indexed third bit):

- Row0 parity flips → becomes `1`.
- Column2 parity flips → becomes `1`.
- Big P changes appropriately.

From the changed row parity (row0) and changed column parity (col2) we locate (row0,col2) and correct it.

#### 2×2 square error (undetected)

Flip bits at positions: (row0,col1),(row0,col2),(row1,col1),(row1,col2)

- Each flipped row sees **two** flips → row parities unchanged.
- Each flipped column sees **two** flips → column parities unchanged.
- Big P remains unchanged.

Result: zero-change in parities → **error undetected**.

---

## 4. Hamming Codes (systematic, single‑error correction)

### Intuition

Hamming codes sprinkle parity bits into the data at positions that are powers of two (1,2,4,8,...). Each parity bit covers particular positions so that the binary index of the error can be reconstructed.

**Rule:** parity bit at position `2^i` covers positions whose binary indexes have the `i`‑th least‑significant bit set.

### How to choose number of parity bits `r` for `m` data bits

Choose smallest `r` such that:

```
2^r >= m + r + 1
```

This ensures that the `r` parity bits can represent all possible single‑bit error positions plus the "no error" state.

### Encoding procedure (even parity, step‑by‑step)

1. Determine `r`.
2. Build a vector of length `m+r` with parity placeholders at positions `1,2,4,8,...` and data in the remaining positions (fill left to right).
3. For each parity position `2^i`, calculate parity as XOR of all bits in positions that have the `i`th bit of their index set (including the parity position itself if you want overall even parity).
4. Parity bits set accordingly; transmit entire `m+r` bits.

### Decoding / correction (receiver side)

1. Recompute parity checks exactly the same way.
2. For each parity bit, if the parity check fails set that parity's weight (2^i) in the syndrome.
3. The binary value of the syndrome gives the **index** of the erroneous bit (0 means no error).
4. Flip that bit to correct.

### Example 1 — Encode & decode `010011011` (your classroom example)

- Data (m) = `010011011` (9 bits). Find `r` such that 2^r >= 9 + r + 1 → r = 4 (since 2^4 = 16 ≥ 14).
- Total length n = 9 + 4 = 13 bits. Parity at positions 1,2,4,8.

**Step — place data:** Positions:  1 2 3 4 5 6 7 8 9 10 11 12 13 Types:      P1 P2 D3 P4 D5 D6 D7 P8 D9 D10 D11 D12 D13 Fill data `0 1 0 0 1 1 0 1 1` into D positions left→right.

**Compute parity bits (even parity):**

- P1 covers positions with LSB = 1: 1,3,5,7,9,11,13 → compute XOR → P1 = 1
- P2 covers positions with 2's bit = 1: 2,3,6,7,10,11 → P2 = 1
- P4 covers positions with 4's bit = 1: 4,5,6,7,12,13 → P4 = 0
- P8 covers positions with 8's bit = 1: 8,9,10,11,12,13 → P8 = 1

**Resulting encoded vector:** `1101100011011` (13 bits)

**Receiver example (single‑bit error):**

- Suppose bit at position 5 flips during transmission. Receiver recomputes parity checks → syndrome = 5. Receiver flips bit at index 5, retrieving original code. (This is the core Hamming correction mechanism.)

Full worked arithmetic for these parity XORs and a sample error/correction are available in the canvas (I included the step calculations).

### Example 2 — Small example `1011` (m=4)

- Choose `r`: for m=4, r=3 satisfies 2^3=8 ≥ 4+3+1=8, so r=3.
- Positions 1..7, parity at 1,2,4. Place data `1 0 1 1` into D positions.
- Compute parity bits (even parity): results in encoded `0110011`.

**Receiver correction demo:** flip bit at position 3 (simulating an error) → syndrome = 3 → corrects bit 3 → recovers `1011`.

**Important Hamming points**

- Corrects exactly **one** bit error.
- Detects when syndrome = 0 (no error), but **multiple** bit errors may produce non‑zero syndromes that do **not** correspond to a single bit (or may produce incorrect corrections if >1 error occurred).

---

## 5. CRC (Cyclic Redundancy Check) — Expanded and Simplified

CRC is *extremely* important in practice. It treats bit sequences as polynomials over the finite field GF(2) and performs polynomial division (modulo 2). The remainder of that division is the CRC value appended to the data.

### What is GF(2)?

* GF(2) stands for **Galois Field with 2 elements** — the simplest finite field.
* Elements are `0` and `1`.
* **Addition in GF(2)** = XOR (so `1 + 1 = 0`).
* **Multiplication in GF(2)** = AND (so `1 * 1 = 1`, everything else `0`).

When we write polynomials over GF(2), coefficients are 0 or 1 only, and subtraction is the same as addition (XOR).

**Examples of polynomials (binary ↔ polynomial):**

* `1011` → polynomial `x^3 + x + 1` (coefficients at positions 3, 1, 0 are 1)
* `101` → polynomial `x^2 + 1`

### CRC Procedure (Step‑by‑Step, Plain Words)

1. **Pick a generator polynomial** `G(x)` (represented in bits). Let its degree be `k`.
2. **Append k zeros** to your data bitstring.
3. **Divide** the extended bitstring by `G(x)` using **mod‑2 division** (XOR instead of subtraction).
4. The **remainder** (k bits) is the CRC value. Append that remainder to the original data (replacing the k zeros) and transmit.
5. **Receiver** divides the received codeword by the same `G(x)`; if remainder ≠ 0 → error detected.

> Note: CRCs are designed so that typical channel error patterns (single-bit errors, double-bit errors, burst errors up to certain lengths) are reliably detected by appropriate generator polynomials.

### What Makes a Good Generator Polynomial?

* Preferably **not factorable** by small polynomials (irreducible / primitive polynomials are useful for maximal-length detection properties).
* Certain polynomials are standardized because they detect common error patterns:

  * `x + 1` (`11`) detects single-bit errors.
  * `x^2 + 1` (`101`) catches many short bursts.
  * `x^3 + x + 1` (`1011`) and `x^4 + x + 1` (`10011`) are common.
  * Ethernet CRC‑32 uses a specific 32-degree polynomial chosen for very strong detection of burst errors.

### Why Mod‑2 Division Looks Like XOR

* In polynomial arithmetic over GF(2), subtraction is the same as addition because coefficients are modulo 2. So the usual long division reduces to a sequence of XORs of overlapping bits.

### Worked CRC Example (Classroom Example)

**Given:**

* Data = `10011011` (8 bits)
* Generator = `x^2 + 1` → polynomial bits `101` (degree k = 2)

**Step 1 — Append k Zeros:**

* `10011011` → `1001101100` (appended two zeros)

**Step 2 — Perform Mod‑2 Division:**

* Division performed via XOR steps yields remainder = `10`.

**Step 3 — Transmitted Codeword = Original Data + Remainder**

* Sent = `10011011 10` → `1001101110`

**Receiver Check**

* Suppose the receiver receives `1101101110` (first data bit flipped). Dividing the received codeword by `101` with non-zero remainder → **error detected**.

### Another CRC Example (Worked)

**Data:** `1101011011`

**Generator:** `10011` (polynomial `x^4 + x + 1`, degree 4)

* Append 4 zeros → `11010110110000`
* Divide by `10011` using mod‑2 long division → remainder `r` (4 bits)
* Transmit `1101011011 r`
* Receiver divides by `10011`; if remainder ≠ 0 → error detected

### Full Long-Division (Mod‑2) for `11010110110000 ÷ 10011`

(Full long-division steps follow — worked for the example.)


We divide the 14‑bit dividend `11010110110000` by the 5‑bit divisor `10011` (generator). Each XOR step is applied when the current leftmost bit is `1`.

Start: dividend = `11010110110000`

Step 0: align divisor under the leftmost 5 bits `11010`

```
11010
10011
----- XOR => 01001
```

Replace positions 0..4 with `01001` → new working bits (showing the whole 14 bits): `01001110110000`

Step 1: move to position 1 — the 5 bits are `10011` (since the bit at pos1 is 1), XOR with divisor:

```
10011
10011
----- XOR => 00000
```

Now bits become `00000010110000`.

Positions 2,3,4,5 have leading 0 → no XOR (we "bring down" bits) until we reach position 6.

Step 6: at position 6 the 5 bits are `10110` → XOR with divisor:

```
10110
10011
----- XOR => 00101
```

Bits become `00000000101000`.

Step 8: at position 8 the 5 bits are `10100` → XOR with divisor:

```
10100
10011
----- XOR => 00111
```

Bits become `00000000001110`.

No more positions to XOR where a full divisor fits — the **remainder** is the last 4 bits (degree of divisor = 4): `1110`.

**So remainder **``**.**

**Transmitted codeword = original data + remainder = **``** → **``**.**

---

### Receiver check (how detection works)

- Receiver gets a 14‑bit codeword (data + CRC). It divides the entire 14‑bit word by `10011` using the same mod‑2 division.
- If the remainder is `0000` → no error.
- If remainder ≠ `0000` → error detected.

**Example of detection:** if one bit flips during transmission, dividing the corrupted codeword will produce a nonzero remainder. For instance, if bit 2 of the transmitted codeword flips, the receiver's remainder will not be `0000` → it flags an error.

**Important note (clarifying confusion from class):** CRC is a **detector**, not a corrector. Standard CRC will not tell you which bit flipped; it only tells you that the received frame is invalid. After detection, typical protocols either request retransmission (ARQ) or drop the frame.

---

## Short summary of what we've completed (to help navigation)

1. **Simple parity** — 1‑bit parity detects odd numbers of bit flips only; cannot correct.
2. **Block (2‑D) parity** — can detect many errors and correct a single flipped bit using intersection of row/column parity; fails for certain symmetric patterns (2×2 square).
3. **Hamming codes** — place parity bits at power‑of‑two positions; can **identify and correct a single bit error** using the syndrome (binary index of error). Examples included for encoding and receiver correction.
4. **CRC** — explained GF(2), why XOR/division works, gave multiple worked examples, and provided full long‑division steps for `1101011011` with generator `10011`.

---
