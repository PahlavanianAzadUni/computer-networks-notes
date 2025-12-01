# **Lesson 7A – Error Control (Deep Dive), Hamming Code, CRC**

In Lesson 6, we explored the concept of **framing** in detail and introduced the fundamentals of **error control** — two of the most essential responsibilities of the **Data Link Layer (Layer 2)**.
In **Part A**, we take a deeper look at error detection and correction mechanisms, supported with practical solved examples.
In **Part B**, we shift our focus to **flow control**, exploring what it is, why it matters, and how real systems use it.

---

## 1. Error Control Recap 🛡️

Error control has two major branches:

* **Error Detection** → finds *whether* an error occurred.
* **Error Correction** → finds *where* the error occurred so it can be fixed.

Methods covered in this lesson: **simple parity**, **2‑D block parity**, **Hamming codes**, **CRC**.

---

## 2. Simple Parity (Quick, limited) 🎯

### Even / Odd Parity

* **Even parity:** choose parity bit so total number of 1s (data + parity) is **even**.
* **Odd parity:** choose parity bit so total number of 1s is **odd**.

### Properties

* Uses **one extra bit** per block.
* Detects any **odd** number of bit errors (1,3,5,…).
* ❌ Cannot detect **even-number** errors.
* ❌ Cannot correct errors — only detects.

### Examples

* Data: `1010110` (ones = 4)

  * Even parity bit = `0` → Transmitted: `1010110 0`
  * Odd parity bit  = `1` → Transmitted: `1010110 1`

If receiver gets `1010111 0`, parity mismatch → **error detected**.

---

## 3. Block Parity (2‑D Parity) 🧩

Block parity arranges bits into a **2‑D grid** (rows × columns). Parity bits are computed for rows, columns, and the final overall parity.

### Structure (your 32‑bit example)

* Divide 32 bits into **4 rows × 8 columns**.
* Row parities: `P0, P1, P2, P3`.
* Column parities: `P-0 ... P-7`.
* Big `P` = XOR of all row and column parities.

```
b00 b01 b02 b03 b04 b05 b06 b07 | P0
b10 b11 b12 b13 b14 b15 b16 b17 | P1
b20 b21 b22 b23 b24 b25 b26 b27 | P2
b30 b31 b32 b33 b34 b35 b36 b37 | P3
--------------------------------------
P-0 P-1 P-2 P-3 P-4 P-5 P-6 P-7 | P
```

### How it detects and corrects

* **Single-bit error** → exactly one row and one column parity mismatch → intersection gives the faulty bit → **correctable** ✅
* **Multiple-bit errors** → usually detectable ❗ but **not always correctable**.
* **2×2 block error** → completely **undetected** ❌ (parities cancel out).

### Concrete numeric example

Rows:

```
Row0: 11001010
Row1: 01101100
Row2: 10110011
Row3: 00011101
```

Row parities:

* P0 = 0
* P1 = 0
* P2 = 1
* P3 = 0

Column parities:
`P-0 = 0, P-1 = 0, P-2 = 0, P-3 = 0, P-4 = 1, P-5 = 0, P-6 = 0, P-7 = 0`

Big P = 0

Final parity block:

```
           ... | 0
           ... | 0
           ... | 1
           ... | 0
---------------------
0 0 0 0 1 0 0 0 | 0
```

#### Single‑bit error demo

Flip Row0, Column2 → row parity flips + column parity flips → error located → corrected.

#### 2×2 square error (undetected) ⚠️

Four flips → each row/column toggles twice → **no change in parities** → undetected.

---

## 4. Hamming Codes (systematic, single‑error correction) 🔍

### Intuition

Parity bits are inserted at **powers of two**: 1, 2, 4, 8…
Each parity bit covers positions whose binary index has that bit set.

### Choosing number of parity bits `r`

Find smallest `r` such that:

```
2^r >= m + r + 1
```

Example: m = 15 → r = 5 because `32 ≥ 20`.

### Encoding procedure (even parity)

1. Determine `r`.
2. Build vector with parity placeholders at **1,2,4,8,...**
3. For each parity bit `2^i`, compute XOR of covered positions.
4. Fill in parity bits and transmit.

### Decoding procedure (receiver)

1. Recompute parity checks.
2. If a parity fails, add its weight to the **syndrome**.
3. Syndrome = index of erroneous bit (0 = no error).
4. Flip that bit → corrected.

### Example 1 — Encode & decode `010011011`

* m = 9 bits → r = 4 → total = 13 bits.
* Parity at 1,2,4,8.

Parity results:

* P1 = 1
* P2 = 1
* P4 = 0
* P8 = 1

Encoded: **`1101100011011`**

Error example: bit 5 flips → syndrome = 5 → flip bit 5 → corrected.

### Example 2 — Small example `1011`

* m=4 → r=3 → positions 1..7.
* Encoded result: **`0110011`**.

Flip bit 3 → syndrome = 3 → fix.

### Important Hamming points ⭐

* Corrects **one single-bit error**.
* Multiple errors may create misleading syndromes → **incorrect corrections possible**.

---
## 🔍 5. CRC (Cyclic Redundancy Check) —



CRC is *extremely* important in practice. It treats bit sequences as polynomials over the finite field GF(2) and performs polynomial division (modulo 2). The remainder of that division is the CRC value appended to the data.



### 🧮 What is GF(2)?



* GF(2) stands for **Galois Field with 2 elements** — the simplest finite field.

* Elements are `0` and `1`.

* **Addition in GF(2)** = XOR (so `1 + 1 = 0`).

* **Multiplication in GF(2)** = AND (so `1 * 1 = 1`, everything else `0`).



When we write polynomials over GF(2), coefficients are 0 or 1 only, and subtraction is the same as addition (XOR).



**Examples of polynomials (binary ↔ polynomial):**



* `1011` → polynomial `x^3 + x + 1` (coefficients at positions 3, 1, 0 are 1)

* `101` → polynomial `x^2 + 1`



### 🧭 CRC Procedure (Step‑by‑Step, Plain Words)



1. **Pick a generator polynomial** `G(x)` (represented in bits). Let its degree be `k`.

2. **Append k zeros** to your data bitstring.

3. **Divide** the extended bitstring by `G(x)` using **mod‑2 division** (XOR instead of subtraction).

4. The **remainder** (k bits) is the CRC value. Append that remainder to the original data (replacing the k zeros) and transmit.

5. **Receiver** divides the received codeword by the same `G(x)`; if remainder ≠ 0 → error detected.



> Note: CRCs are designed so that typical channel error patterns (single-bit errors, double-bit errors, burst errors up to certain lengths) are reliably detected by appropriate generator polynomials.



### 🧠 What Makes a Good Generator Polynomial?



* Preferably **not factorable** by small polynomials (irreducible / primitive polynomials are useful for maximal-length detection properties).

* Certain polynomials are standardized because they detect common error patterns:



  * `x + 1` (`11`) detects single-bit errors.

  * `x^2 + 1` (`101`) catches many short bursts.

  * `x^3 + x + 1` (`1011`) and `x^4 + x + 1` (`10011`) are common.

  * Ethernet CRC‑32 uses a specific 32-degree polynomial chosen for very strong detection of burst errors.



### 🔗 Why Mod‑2 Division Looks Like XOR



* In polynomial arithmetic over GF(2), subtraction is the same as addition because coefficients are modulo 2. So the usual long division reduces to a sequence of XORs of overlapping bits.



### ✏️ Worked CRC Example (Classroom Example)



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



### ✨ Another CRC Example (Worked)



**Data:** `1101011011`



**Generator:** `10011` (polynomial `x^4 + x + 1`, degree 4)



* Append 4 zeros → `11010110110000`

* Divide by `10011` using mod‑2 long division → remainder `r` (4 bits)

* Transmit `1101011011 r`

* Receiver divides by `10011`; if remainder ≠ 0 → error detected



### 📘 Full Long-Division (Mod‑2) for `11010110110000 ÷ 10011`



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

---



### 📡 Receiver Check (how detection works)



- Receiver gets a 14‑bit codeword (data + CRC). It divides the entire 14‑bit word by `10011` using the same mod‑2 division.

- If the remainder is `0000` → no error.

- If remainder ≠ `0000` → error detected.



**Example of detection:** if one bit flips during transmission, dividing the corrupted codeword will produce a nonzero remainder. For instance, if bit 2 of the transmitted codeword flips, the receiver's remainder will not be `0000` → it flags an error.



**Important note :** CRC is a **detector**, not a corrector. Standard CRC will not tell you which bit flipped; it only tells you that the received frame is invalid. After detection, typical protocols either request retransmission (ARQ) or drop the frame.



---



## 📝 Short Summary of what we've completed (to help navigation)



1. **Simple parity** — 1‑bit parity detects odd numbers of bit flips only; cannot correct.

2. **Block (2‑D) parity** — can detect many errors and correct a single flipped bit using intersection of row/column parity; fails for certain symmetric patterns (2×2 square).

3. **Hamming codes** — place parity bits at power‑of‑two positions; can **identify and correct a single bit error** using the syndrome (binary index of error). Examples included for encoding and receiver correction.

4. **CRC** — explained GF(2), why XOR/division works, gave multiple worked examples, and provided full long‑division steps for `1101011011` with generator `10011`.



---
