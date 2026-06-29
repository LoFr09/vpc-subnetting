# Explain-Back

## 1. Rechnung Subnet B (60 Hosts)

60 + 5 = 65. Nächste Zweierpotenz ist 128 → /25. Nutzbar: 128 − 5 = 123.
5 sind weg, weil AWS pro Subnetz 5 Adressen reserviert.
/26 wäre zu klein (64 − 5 = 59, reicht nicht für 60). /24 ginge, verschwendet aber
Platz. Darum /25.

## 2. Warum nur Subnet A ins Internet kann

Die Route zum IGW (0.0.0.0/0) ist nur bei Subnet A. B und C haben sie nicht.
Eine Instanz in C kann darum nur intern reden, nicht ins Internet. So will man das
bei Management.

## 3. NACL vs Security Group

NACL = zustandslos. Inbound und Outbound werden getrennt geprüft.
Security Group = zustandsbehaftet, lässt die Antwort automatisch zurück.
Darum brauche ich bei der NACL die Outbound-Regel 1024-65535. Bei einer Security
Group bräuchte ich die nicht.

## 4. Explizites Deny am Ende

Macht dasselbe wie das eingebaute *, ist aber klar sichtbar und ich kann es selbst
platzieren. Sauberer.