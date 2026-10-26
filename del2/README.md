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

Så satte jeg en kabel mellom Router Admin og Router Salg. Og kunfigurerte portene mellom ruterne. Jeg ga giga 0/1 på Admin ip 10.0.0.1 /29 og giga 0/1 på Salg 10.0.0.2 /29 
