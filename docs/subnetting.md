# Subnetting

# Aufteilung

Ich habe es so gelernt, man jedes Oktet hat 8.
8 + 8 + 8 + 8 = /32
Wenn man nun nur das minmum möchte, zähl man die Zahl in Binär, pro Oktet hat es 254 verschiedene Hosts

Wie habe ich es gerechnet:


**Public/Web**  
Braucht 250 Adressen heisst /24 wieso ? 255.255.255.0 = 255 verfügbar.

**App** 
Braucht 60 Adressen heisst /26 wieso?
255.255.255.192 = 63 Freie Adressen

**Management** 
Braucht 12 Adressen heisst /28 wieso ?
255.255.255.240 = 15 Freie Adressen. 


# Übersicht

**Public/Web:** 10.0.1.0/24
**App:** 10.0.2.0/26
**Management:** 10.0.3.0/28

So überschneiden sie sich nicht und brauchen nicht unnötig Platz.


***Nicht sicher*** ich bin mir nicht sicher ob ich auch zb einfach bei Public/Web 10.0.1.250 und dan den block App bei 10.0.1.251 beginnen und bis 10.0.2.56 und dan Management 10.0.2.57 bis 10.0.2.69. 
Bei so einer Situation würde ich an den Regios einfach googlen oder KI Fragen das ich auf nummer sicher gehe und keine Punkte verschenke.