# Subnetting

## So habe ich gerechnet

Eine IP hat 32 Bit. Das Präfix (z.B. /24) sagt, wie viele Bit fürs Netz sind, der
Rest sind Hostbits. Adressen im Block = 2 ^ Hostbits.

In AWS sind pro Subnetz **5 Adressen weg** (Netz, Router, DNS, reserviert, Broadcast).
Darum rechne ich rückwärts: **nutzbare Hosts + 5**, dann die nächste Zweierpotenz.

## Die drei Subnetze

**Public/Web — 10.0.1.0/24**
250 + 5 = 255 → 256 Adressen → /24
256 − 5 = **251 nutzbar**

**App — 10.0.2.0/25**
60 + 5 = 65 → 128 Adressen → /25 (64 reicht nicht, weil 64 − 5 = 59 < 60)
128 − 5 = **123 nutzbar**

**Management — 10.0.3.0/27**
12 + 5 = 17 → 32 Adressen → /27 (16 reicht nicht, weil 16 − 5 = 11 < 12)
32 − 5 = **27 nutzbar**

## Übersicht

| Subnet | CIDR | Bereich | Nutzbar |
|---|---|---|---|
| Public/Web | 10.0.1.0/24 | 10.0.1.0 – .255 | 251 |
| App | 10.0.2.0/25 | 10.0.2.0 – .127 | 123 |
| Management | 10.0.3.0/27 | 10.0.3.0 – .31 | 27 |

Keine Üeberlappung, alles in 10.0.0.0/16.

Ein Block startet immer auf einer Netzgrenze (/25 nur auf Vielfachen von 128, /27 nur
auf Vielfachen von 32). Darum kann man nicht einfach mitten drin starten.