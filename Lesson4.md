# 📘 **Lesson 4 — Switching Review, Virtual Circuits, Datagrams, and Signals**

---

# 🔄 **Part 1 — Deep Review (Building upon what was said in Session 3)**

This session started by reinforcing the core ideas from **Session 3**, especially the *flow of communication*, *components*, and *signal behavior*. The following review is rewritten so that there will be  **no need to jump back to Session 3**.

---

## 🧩 **What Is a Signal? (Review + Expansion)**

A **signal** is the physical form of data as it travels through a medium.

In Session 3 it was said that:

> **“Signal is the representation of data during transfer.”**

In Session 4, the professor expanded this into two key dimensions:

### **1. Nature of the Signal**

* **🟦 Analog Signal** → continuous, smooth, infinite values
* **🟧 Digital Signal** → discrete, step-like, finite values

### **2. Shape / Physical Form**

Signals can exist as:

* 🔌 **Electrical** (copper wires)
* 📡 **Electromagnetic** (Wi-Fi, radio)
* 💡 **Light** (fiber optic)
* 🔊 **Sound waves** (less common in networking)

---

## 🔁 **Reviewing the Communication Process**

Session 3 introduced the classic communication chain. Session 4 reiterated it with more examples.

```
┌──────────┐      ┌──────────┐      ┌──────────┐      ┌────────────┐      ┌──────────┐
│  Sender  │ ---> │ Encoder  │ ---> │ Channel  │ ---> │ Decoder    │ ---> │ Receiver │
└──────────┘      └──────────┘      └──────────┘      └────────────┘      └──────────┘
```

### 📝 Key reminders (from Session 3)

* The **sender** has *data*.
* The **encoder** turns data → signal.
* The **channel** carries the signal.
* The **decoder** turns signal → data.
* The **receiver** gets the final data.

Session 4 emphasized:

> **“Anything that affects the signal *inside the channel* affects communication reliability.”**

---

## 🟨 **Noise**

Unwanted *random* disturbance that naturally occurs in any communication channel.

* Comes from **thermal effects**, **electronics**, **background randomness**.
* Cannot be eliminated, only reduced.
* Always present.

Examples:

* Thermal noise in copper 
* Random amplitude spikes
* Unpredictable interference

---

## ⭐ **Interference**

Interference refers to deliberate, external, intentional or semi‑intentional disturbance.

This includes:

* EMI (Electromagnetic Interference)
* RFI (Radio Frequency Interference)
* Crosstalk (one cable leaking into another)

These are **not random** — they come from identifiable external sources.


Examples:

* A microwave interfering with Wi‑Fi 
* Two unshielded cables disturbing each other
* A radio tower saturating nearby frequencies

Session 4 explicitly taught us that:

> **Noise is natural. Interference is external and often predictable.**

Here is a quick comparison chart:

| Concept           |  English Term | Behavior                    | Example                    |
| ----------------- | ------------ | --------------------------- | -------------------------- |
| 🔈 Noise          |  Noise        | Random, always present      | Thermal noise in copper    |
| 📡 Interference   | Interference | External, identifiable      | Microwave disturbing Wi‑Fi |
| 🔗 Crosstalk      |  Crosstalk    | One wire leaks into another | UTP pairs interfering      |

---

# 🔌 **Media Types (with Visual Diagrams)**

Your professor reviewed this in Session 3 but expanded heavily in Session 4. Here are improved diagrams.

---

## 1️⃣ **Twisted Pair Cable (UTP / STP)**

```
    UTP Cable Structure

    ┌──────────────────────────────┐
    │   (No metal shielding)       │
    │   Pairs twisted to reduce    │
    │   crosstalk                  │
    └──────────────────────────────┘
         ╱╲   ╱╲   ╱╲   ╱╲
        ╱  ╲ ╱  ╲ ╱  ╲ ╱  ╲   ← twisted copper pairs
```

✨ Notes:

* Cheap, flexible
* Sensitive to **interference** (پارازیت)
* Uses **RJ‑45** connector

---

## 2️⃣ **Coaxial Cable (کواکسیال)**

```
       Coaxial Cable Layers

      ┌──────────────────────────────┐  Outer Insulation
      │  ┌────────────────────────┐  │
      │  │   Braided Shield       │  │
      │  └────────────────────────┘  │
      │      ┌───────────────────┐   │
      │      │   Dielectric      │   │
      │      └───────────────────┘   │
      │          ● Inner Core        │  Copper
      └──────────────────────────────┘
```

✨ Notes:

* Very resistant to interference
* Common in early Ethernet and cable TV
* Expensive compared to UTP

---

## 3️⃣ **Fiber Optic Cable (فیبر نوری)**

```
         Fiber Optic Structure

        ┌─────────────────────────────┐  Outer Jacket
        │ ┌─────────────────────────┐ │  Strength Fibers
        │ │  Cladding (reflective)  │ │
        │ │  ┌────────────────────┐ │ │
        │ │  │    Core (glass)    │ │ │  ← Carries light
        │ │  └────────────────────┘ │ │
        | └─────────────────────────┘ |
        └─────────────────────────────┘
```

✨ Notes:

* Immune to noise and interference
* Very high bandwidth
* Light signals, not electricity

---

# 🔬 **Session 4 Additional Clarifications**

## ⭐ Why Do We Twist the Wires in UTP?

Because twisting creates opposing electromagnetic fields that **cancel each other**, reducing **crosstalk**.

Emoji version:

* 🔄 Twist → 🔇 Less interference → 🚀 Better signal

---

## ⭐ Why Is Fiber Optic Immune to Noise?

Because it uses **light**, not electricity.

* No EMI
* No crosstalk
* No electrical distortion

---

## ⭐ What Affects Signal Quality?

From Sessions 3 + 4:

* Distance 📏
* Interference 📡
* Attenuation 📉
* Noise 🔈
* Cable quality 🔌
* Environment 🌧️⚡

---

# 🎯 **Summary + What You Should Remember**

### From Session 3 (integrated here):

* Signals represent data
* Channel has encoder → medium → decoder
* Both analog and digital signals exist

### From Session 4 (expanded here):

* Strong distinction between **noise** and **interference (پارازیت)**
* Different physical media shape different types of signals
* Cable structure affects susceptibility to interference
* Fiber uses light and avoids electrical problems entirely

---

