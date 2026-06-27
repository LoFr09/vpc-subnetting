# Aufgabe: VPC, Subnetting und Network ACL

**Block:** Skill 53 — Networking / VPC-Design
**Betroffene AWS Services:** VPC, Subnets, Internet Gateway, Route Tables, Network ACLs
**KI-Regel:** Manual First — die CIDR-Berechnung machst du selbst, mit Stift und Papier oder Taschenrechner. Keine KI, kein Online-Subnet-Calculator für die Herleitung (zur Kontrolle des Ergebnisses danach ist ein Calculator erlaubt).
**Voraussetzung:** Aktiver AWS-Account, Grundverständnis IP-Adressen, Subnetzmasken und CIDR-Notation.
**Zeit:** 4h

---

### Summary

Du baust für einen Kunden ein abgegrenztes Netz in AWS auf. Das Netz soll drei Subnetze haben, jedes für einen anderen Zweck und mit unterschiedlich vielen Hosts. Eine bequeme Lösung wäre, dreimal das gleiche `/24` zu nehmen und den Adressraum zu verschwenden. Diese Aufgabe verlangt das Gegenteil: Du rechnest die Subnetze so zu, dass jedes genau so gross ist wie nötig, und du begründest jede Maske schriftlich.

Das ist bewusst Kopfarbeit. Subnetting ist exakt die Art von präziser, schriftlicher Aufgabe, bei der ein Rechenfehler nicht „ungefähr stimmt", sondern das ganze Design kippt — überlappende Bereiche, zu kleine Subnetze, verschwendeter Raum. Wer die Herleitung sauber aufschreiben kann, hat verstanden, was die Konsole sonst nur anklicken lässt.

---

### Task

1. Lege eine VPC mit dem CIDR-Block `10.0.0.0/16` an.
2. Berechne und erstelle **drei Subnetze** mit folgenden Host-Anforderungen — die nötigen CIDR-Blöcke leitest du selbst her:
   - **Subnet A (Public/Web):** mindestens 250 nutzbare Hosts
   - **Subnet B (App):** mindestens 60 nutzbare Hosts
   - **Subnet C (Management):** mindestens 12 nutzbare Hosts
   Die drei Subnetze dürfen sich nicht überlappen und sollen den Adressraum nicht unnötig verschwenden (wähle für jedes die kleinste Maske, die die Anforderung erfüllt).
3. Erstelle ein **Internet Gateway** und hänge es an die VPC.
4. Erstelle eine **Route Table** mit einer Default-Route (`0.0.0.0/0`) zum Internet Gateway und assoziiere sie ausschliesslich mit **Subnet A** (dem Public-Subnet). Subnet B und C bleiben ohne Internet-Route.
5. Erstelle eine **Network ACL** und assoziiere sie mit Subnet A. Sie soll **nur HTTP (80) und HTTPS (443)** als eingehenden Verkehr erlauben und allen anderen eingehenden Verkehr explizit verweigern. Denk an die Rückrichtung (ausgehender Verkehr / ephemeral ports) — eine NACL ist zustandslos, anders als eine Security Group.
6. Dokumentiere die CIDR-Berechnung **schriftlich und nachvollziehbar**: Für jedes Subnetz die geforderte Host-Zahl, die gewählte Präfixlänge, die Anzahl resultierender Adressen, die Anzahl davon **nutzbarer** Hosts (denk an die in AWS reservierten Adressen pro Subnetz), und der konkrete CIDR-Block. Nicht nur das Ergebnis — den Rechenweg.

---

### Verification

- Die drei Subnet-CIDRs liegen innerhalb von `10.0.0.0/16` und überlappen sich nicht. Prüfe das, indem du Anfangs- und End-Adresse jedes Blocks ausrechnest.
- In der VPC-Konsole zeigt jedes Subnet die von dir berechnete Anzahl „Available IPv4 addresses" — vergleiche mit deiner Rechnung. Weicht es ab, hast du die in AWS reservierten Adressen pro Subnetz vergessen.
- Route Table: Nur Subnet A ist mit der Internet-Route assoziiert. Prüfe in der Route-Table-Ansicht die „Subnet associations".
- Network ACL: Ein Verbindungstest auf Port 80/443 zu einer Ressource in Subnet A kommt durch, ein Test auf einem anderen Port (z.B. 22) wird durch die NACL geblockt. Wenn HTTPS nicht antwortet, prüfe deine Outbound-Regeln (ephemeral ports).

---

### Hints

- AWS reserviert in **jedem** Subnetz eine feste Anzahl Adressen, die du nicht für Hosts nutzen kannst. Wie viele es sind und welche, steht in der VPC-Doku zu Subnet-Sizing — diese Zahl gehört zwingend in deine „nutzbare Hosts"-Rechnung, sonst rechnest du zu knapp.
- Gehe von der Host-Anforderung rückwärts: Wie viele Adressen brauchst du mindestens (inklusive der reservierten)? Welche Zweierpotenz deckt das gerade noch ab? Daraus ergibt sich die Hostbits-Anzahl und damit die Präfixlänge.
- Eine Network ACL ist **zustandslos**: Eine erlaubte eingehende Verbindung hat keine automatisch erlaubte Antwort. Die Rückantworten gehen auf ephemeral ports raus — wenn du die Outbound-Regel vergisst, kommt die HTTP-Antwort nicht zurück, obwohl Inbound korrekt ist. Das ist der klassische NACL-Stolperstein und genau der Unterschied zur Security Group.
- NACL-Regeln werden nach Regelnummer ausgewertet, die erste passende greift. Überlege dir die Reihenfolge bewusst, bevor du das explizite Deny setzt.

---

### Explain-Back

Schriftlich in eigenen Worten.

1. Zeig deine Rechnung für **Subnet B** (60 Hosts): Wie viele Adressen brauchst du mindestens, welche Präfixlänge ergibt sich, wie viele Hosts sind am Ende nutzbar und warum nicht alle Adressen des Blocks? Was wäre passiert, wenn du eine Präfixlänge kleiner gewählt hättest — und was bei einer grösseren?
2. Warum hat in deinem Design nur Subnet A eine Route zum Internet Gateway, Subnet B und C aber nicht? Was bedeutet das praktisch für eine Instanz in Subnet C?
3. Erkläre an deiner Network ACL den Unterschied zwischen zustandslos (NACL) und zustandsbehaftet (Security Group). Welche konkrete Regel in deiner NACL hättest du bei einer Security Group **nicht** gebraucht, und warum?
4. Deine NACL hat ein explizites Deny am Ende, obwohl eine NACL am Schluss ohnehin alles Nicht-Erlaubte verwirft. Wann ist ein explizites Deny trotzdem sinnvoll, und worin unterscheidet es sich vom impliziten Verwerfen?

---

### Abgabe

- Eigenes Git-Repo, **ein Commit pro Schritt**.
- `docs/subnetting.md` mit der vollständigen, nachvollziehbaren CIDR-Herleitung pro Subnetz (Rechenweg, nicht nur Ergebnis).
- `docs/vpc_aufbau.md` mit der Beschreibung von IGW, Route Table und NACL in eigenen Worten.
- Screenshots als Nachweis: VPC-Übersicht mit den drei Subnetzen und ihren „Available IPv4 addresses", Route-Table mit Subnet-Association, NACL-Regeln (Inbound + Outbound).
- `docs/explain_back.md` mit den vier Antworten.
