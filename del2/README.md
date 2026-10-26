I denne delen skal vi late som vi jobber for Fjordtek AS og skal bygge og sette opp nettverket deres. Sånn jeg forestiller meg det er Salgs avdelingen er i en etasje og admin og support er i etasjen over. Vær avdeling skal ha sitt eget nettverk. Jeg tenker det skal se omtrent slikt ut:
<img width="730" height="525" alt="del2 drawio" src="https://github.com/user-attachments/assets/e53a076c-eb76-41bb-a4d5-392672eb6b37" />
Hvor vær avdeling kan koble seg til switchen på deres nettverk, Jeg tenker at salgs avdelingen er veldig stor så gir dem mange mange switcher å koble seg til.

Jeg tenker å gi:
Admin: 192.168.10.0 /24
Salg: 192.168.20.0 /24
Support: 192.168.30.0 /24
Server: 192.168.40.0 /24

Jeg åpnet så packet tracer og plaserte switcher, rutere og en server, men ventet med å koble dem sammen før jeg skulle bruke dem for å ha en bedre oversikt over hvilke porter som ble brukt av hva. 
Først satte jeg opp admin nettverket.
Satte navnet på en ruter til Router Admin
koblet den til en switch som jeg igjen koblet til to PCer
Satt statisk ip på begge PCene til 192.168.10.101 og x.x.x.102 med mask 255.255.255.0 og testet om de kunne pinge hverandre. det funket
Så konfigurerte jeg Admin ruteren i CLI og skrev:
1. enable
2. configure terminal
3. interface gigabitEthernet 0/0 (fordi det var den utgangen som var koblet til admin switch)
4. ip address 192.168.10.1 255.255.255.0
5. no shutdown
6. end
Da vil ruteren vite at den porten er til 192.168.10.0 nettverket. Jeg testet om jeg kunne pinge den fra en PC og det fungerte.

Så satte jeg opp salgs nettverket på samme måte, bare med 192.168.20.x IPer og testet at jeg kunne pinge andre PCer og ruteren. Den ruteren ga jeg navnet Router Salg.

Så satte jeg en kabel mellom Router Admin og Router Salg. Og kunfigurerte portene mellom ruterne. Jeg ga giga 0/1 på Admin ip 10.0.0.1 /29 og giga 0/1 på Salg 10.0.0.2 /29. Det er de portene som holder kabelen mellom ruterene.
Så satte jeg statiske ruter. For det åpnet jeg CLI på Router Admin og skrev som følge:
1. enable
2. configure terminal
3. ip route 192.168.20.0 255.255.255.0 10.0.0.2 (Det gjør at hvis ruteren får en pakke som skal til 192.168.20.0 adressen sendes den til 10.0.0.2)
4. End
Fordi Router Salg bare har et nettverk som er tilkoblet er det nok

Så gjorde jeg det samme på Router Salg, her må jeg legge til admin og support

Så bygget jeg Support netverket på samme måte som salg og admin, altså configurere ruter porten mot support (giga 0/2) og satt statisk IP på PCene.
Support har som nevnt 192.168.30.x adresser.
Så testet jeg ved å pinge på PCene til andre PCer på andre nettverk og det fungerte.
Nå tenkte jeg å koble serveren til Router Admin, men jeg var tom for Porter, så etter litt fram og tilbake endte jeg med å sette inn en "HWIC-1GE-SFP" modul inn i ruteren og så igjen sette en "GLC-LH-SMD" port i den. Og for at den utgangen skal passe til Router Salg måtte jeg gjøre det samme på den. Så nå ser portene slik ut:
Admin Ruter til admin nettverk går over port giga 0/0
Admin R til support nettverk giga 0/1
Admin R til server giga 0/2
og Admin og Salg R har begge giga 0/0/0 mot hverandre
Salg R til salgs nettverk giga 0/0

DHCP er greit å ha så det satte jeg opp på ruterne. Det gjorde jeg i CLI på begge ruterne og det gjorde jeg med å skrive:
1. configure terminal
2. ipdhcp excluded-address 192.168.10.1 192.168.10.20 (Det gjør så den ikke den tilgi adresser fra og med 192.168.10.1 til 192.168.10.20)
3. ip dhcp pool ADMIN
4. network 192.168.10.0 255.255.255.0
5. default-router 192.168.10.1
6. end
Med det får PCene på admin nettverket DHCP, så gjorde jeg det samme på Router Admin for support, og på Router Salg for salg.
Når jeg da slo på DHCP på alle PCene fikk de en unik IP.
