# Aufbau des VPC

## Internet Gateway
- igw-08fa4e60b699c9bb7, attached an my-vpc-01

Das IGW ist die Tür der VPC nach aussen. Ohne IGW gibt es keinen Weg ins Internet.

## Route Table
- rtb-0e1bb128c4582a232, nur mit Subnet A verbunden

| Destination | Target |
|---|---|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | igw-08fa4e60b699c9bb7 |

0.0.0.0/0 schickt alles, was nicht in die VPC gehört, ans IGW. Nur Subnet A hat
diese Route. B und C nutzen die Main Route Table (nur local) und kommen darum nicht
ins Internet. Das ist gewollt.

## Network ACL
- acl-04ddf2014316ea60a, an Subnet A

Die NACL ist eine Firewall pro Subnetz und **zustandslos**: Inbound und Outbound
werden getrennt geprüft. Regeln nach Nummer, die erste passende greift.

Inbound: 80 Allow, 443 Allow, Rest Deny.
Outbound: 1024-65535 Allow, Rest Deny.

Warum Outbound: Der Server antwortet nicht auf 80/443 zurück, sondern an den hohen
(ephemeral) Port des Clients. Weil die NACL zustandslos ist, braucht dieser Rückweg
eine eigene Regel. Ohne sie kommt die Antwort nie beim Client an.