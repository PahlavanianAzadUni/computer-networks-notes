# Bus Topology

## Structure
All devices share a **single communication line (bus)** — a common cable where data flows in both directions.

A ---- B ---- C ---- D


## How It Works
- Each node listens to the bus.
- When one node transmits, the signal travels along the bus to all nodes.

## Problems
- **Collisions**: If two devices send data simultaneously, signals overlap and corrupt each other.
- **Difficult Troubleshooting**: A fault in the main cable can disable the entire network.
- **Limited scalability**: Adding devices increases collisions.

## Collision Handling
Methods to avoid or manage collisions:
1. **Time Division Multiplexing (TDM)** – devices take turns sending data based on time slots.
2. **Frequency Division Multiplexing (FDM)** – each device transmits on a unique frequency band.
3. **Carrier Sense Multiple Access (CSMA/CD)** – used in early Ethernet; devices listen before sending.

## Summary
✅ Simple, cheap, easy to set up.  
❌ Poor performance, collision-prone, not used in modern LANs.
