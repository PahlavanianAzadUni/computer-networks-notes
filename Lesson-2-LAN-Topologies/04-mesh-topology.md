# Mesh Topology

## Structure
Every device is directly connected to every other device.

A---B
|\ /|
| X |
|/ |
C---D


## Characteristics
- Highly redundant and reliable.
- Very expensive and complex (number of connections grows rapidly).

## Formula
For *n* devices, total links = **n × (n − 1) / 2**

## Pros
- No single point of failure.
- Extremely robust (common in backbone or high-security systems).

## Cons
- Impractical for large networks (too many cables).
- Complex configuration.

> Mesh networks are often used **partially** (hybrid mesh) — not every device connects to every other, only key nodes.
