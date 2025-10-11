# Ring Topology

## Structure
Devices are connected in a closed loop — each with two links: one to send, one to receive.

A → B → C → D → A


## How It Works
- Data travels in one direction (clockwise or counterclockwise).
- Each node receives data, checks the address, and passes it along.
- When data returns to sender, it is removed from the ring.

## Pros
- Predictable data flow, no collisions (only one token at a time can circulate).
- Good for orderly transmission.

## Cons
- Failure in one node or cable breaks the ring.
- Each device needs **two network cards (two IPs)**.
- Maintenance and troubleshooting are complex.

## Improvements
- **MAU (Multi-Access Unit)** — simulates a ring but works like a central smart hub:
  - Each device has a send and receive path.
  - MAU directs signals only to the correct receiver.
  - Removes the need for multiple IPs per device.
