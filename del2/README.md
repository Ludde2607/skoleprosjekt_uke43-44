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
Så configurerte jeg ruteren, satt IP til 192.168.40.10, mask til 255.255.255.0 og default gateway til 192.168.40.1.
For å gjøre noe med serveren skrudde jeg på HTTP og endret litt på index.html så det sto Velkommen til FjordTek AS på siden. Så testet jeg om det fungerte ved å prøve å gå in på 192.168.40.10 i web browseren på serveren. Det funket, så testet jeg på en annen PC og det fungerte også. Nå tenkte jeg at det å skrive inn IP er litt kjedelig så jeg tenkte å gjøre det til noe annet ville være kult. For det måtte jeg ha DNS og det valgte jeg å gjøre på samme server (192.168.40.10). Da enablet jeg DNS service og under navn skrev jeg intranett.fjordtek.local og satte address til 192.168.40.10. Når jeg da skrev inn intranett.fjordtek.local i web browser på serveren kom jeg til siden, men på en annen PC i systemet fungerte det ikke. Og det var fordi de ikke viste at serveren var en DNS server. De kunne jeg ha konfigurert manuelt på vær PC, men da måtte jeg også hatt en statisk IP adresse på den og ikke brukt DHCP og det vill jeg ikke. Jeg var usikker på hva jeg måtte gjøre for å få den til å bli satt automatisk, men siden jeg ikke kunne sette den statisk med DHCP på så tenkte jeg at det hadde noe med det å gjøre. Og det hadde det. Så med litt undersøkelse fant jeg ut at hvis jeg åpner CLI på ruterne og skriver:
1. configure terminal
2. ip dhcp pool ADMIN (SALG/SUPPORT for de andre)
3. dns 192.168.50.10
4. end

Så skrude av og på DHCP på vær PC fikk de en DNS Server registrert også. Og nå fungerte intranett.fjordtek.local i web browser på alle.
Nå ville oppgaven at jeg skulle simulere noe på netverket mitt

Krav: At to PCer på samme LAN kunne kommunisere
Test: Brukte "sample PDU" funksjonen og trykket på support1 og 2.
Forventet resultat: En pakke går fra Support1 til switch, så switch til Router og Support2, Router dropper den og Support2 tar den imot. Så sender Support et svar tilbake.
Faktisk resultat: bestått

Krav: Teste kommunikasjonen mellom Admin og Salg.
Test: Brukte "sample PDU" og trykket på Admin1 og Salg1.
Forventet ressultat: Pakken går til Admin Switch, så videre til Router Admin (192.168.10.1) så til Router Salg, som er vill være 10.0.0.2 så ut til Salg hov switch, Salg S 2, så Salg1 (192.168.20.23), sender Salg1 et svar til 192.168.20.1 (Router Salg), som så sender til 10.0.0.1 (Router Admin), så til 192.168.10.22 (Admin1).
faktisk resultat: bestått

Krav: Åpne intranettsiden med DNS-navn og se hvordan DNS og HTTP inngår i samme brukerhandling.
Test: Åpne web browser på Admin1 og gå til intranett.fjordtek.local.
Forventet resultat: Først sender Admin1 en forespørsel til DNS server (192.168.40.10) om hva IPen til intranett.fjordtek.local er. DNS server gir svar og forteller at det er 192.168.40.10, Admin1 spør så om å få tilgang til 192.168.40.10 sin nettside.
Faktisk resultat: bestått

Oppgaven var krevende, men overkommelig. Det å gjøre oppgaven var ok, men det å dokumentere er jeg usikker på om er bra. Beskriver jeg for mye? for lite? etc. Syntes det ble bra. Brukte også Kanban for å stykke opp oppgaven, men likte ikke å bruke det for denne type oppgave. Tror jeg hadde likt bedre å bruke det om det var en oppgave over en lenger periode, syntes det ble veldig Flytt fra "To do" til "in progress" jobbe 20-30 min så flytte til "Done" så jeg enten glemte å oppdatere den rett etter jeg var ferdig med noe eller oppdaterte rett etter jeg var ferdig og da miste flyten i arbeide jeg jobbet med, det la jeg spesielt merke til på Del 2.
