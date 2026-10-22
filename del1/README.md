Jeg skal sette opp mitt eget nettverk på skolen. Det første jeg gjorde var å lese raskt gjennom oppgaven og lagde en kanban på github og fylte inn i kanban det jeg tenkte at måtte gjøres og brukte det for å lage en plan. Og under prosjektet la jeg til og flyttet rundt på de for å ha kontroll på hva jeg holdt på med fra dag til dag. Jeg tegnet også opp hvordan jeg tenkte at nettverket mitt skulle se ut i draw.io som er første bilde som kommer etter.

Det jeg har tilgang til av utstyr er:
1 ruter
1 switch
1 laptop (klient pc)
1 lap pc (server)

<img width="461" height="480" alt="mittnetverkoppsett drawio" src="https://github.com/user-attachments/assets/5f631eea-d741-43b0-a50f-8f7ded568e9a" />

Mitt oppsett ser slik ut

Oscar og jeg hadde bare tilgang til en WAN port i veggen så slik at begge skulle ha murlighet til å få internett på sitt nettverk og for å holde de mest murlig separate spurte vi om vi kubbe få en switch til. Selv om det ikke er en del av mitt nettverk tenker jeg det er greit å ta med i dokumentasjonen.

Først plugget jeg alt sammen med ethernet kabeler slikt som i bildet, alt med kabel fra switch til server, klient og router. Og kabel fra den delte switchen til WAN porten i ruteren.

Fordi vi allerede hadde satt opp en server tidligere tenkte jeg det var smart å bruke det som var satt på den som utgangspunkt.
Den har:
IP: 192.168.1.10
Mask: 255.255.255.0
Default gateway: 192.168.1.1
DNS:192.168.1.10 siden vi har DNS på serveren
Så IP-en 192.168.1.0 med mask 255.255.255.0 tenkte jeg å bruke som mitt nettverk.

Deretter satte jeg opp ruteren, den fikk jeg den dagen vi startet med prosjektet.
Det jeg gjorde med den var som følge:
1. Resatte den
2. Lagde et passord til den på tplinkwifi.nett og logget meg på
3. Slo av wireless 2.4 og 5 fordi jeg ikke tenke å bruke det.
4. Konfigurerte LAN insilingene til:
   IP: 192.168.1.1 fra 192.168.0.1 (default)
   Mask: 255.255.255.0

Tilslutt konfigurerte jeg IPen på klient pc til:
Statisk IP: 192.168.1.100
Mask: 255.255.255.0
Default gateway: 192.168.1.1
DNS: 192.168.1.10

Deretter kontrolerte jeg at alt fungerte med å ta en "ipconfig", "ping 192.168.1.1" (ruteren) og "ping 192.168.1.10" (serveren) i CMD for å sjekke at alt fungerte som det skulle og evt om noen av kablene var defekte eller at alt av IP etc var satt riktig.
Da fikk jeg dette resultatet:
<img width="217" height="425" alt="image2" src="https://github.com/user-attachments/assets/fe50456f-0774-4a07-80f4-2940f11430b1" />
Jeg hadde klart å satt riktig IP på klient pcen og fikk svar når jeg pinget ruter og server, så alt ser ut som skal fungere.

Deretter gikk jeg inn på tplinkwifi.net for å sjekke DHCP innstillingene på ruteren, den var på og hadde en range på 100-199 som vil si at den kan dele ut de IP adressene etter forespørsel.
For å teste at DHCP fungerte byttet jeg fra statisk til automatisk. Da frøs "nettverkstilkoblinger", men ved å bare restarte maskinen funket det igjen og jeg kunne se at den var på automatisk. Så sjekket jeg at jeg hadde fått tildelt en adresse og det hadde jeg. 
<img width="490" height="110" alt="image3" src="https://github.com/user-attachments/assets/a098c5d5-f6e6-4b61-b554-2243c62dc958" />
Jeg fikk 192.168.1.102 så DHCP fra ruteren fungerte.
Fordi jeg jobber med prosjektet over flere dager vill den IPen endres gjennom oppgaven, men ruter vill alltid være .1, server vill alltid være .10 og klient PCen vill altid være mellom .100 og .199

Dersom jeg skulle trenge å dokumentere noe fra serveren tenkte jeg det var smart å sette opp en delt mappe så jeg lett kunne overføre bilder mellom maskinene. Mappen lagde jeg bare på skrivebordet til serveren, slo på shareing og endre på security og ga tilgang til alle til å legge til, endre og fjerne filer i mappen.
Så på klient pcen koblet jeg meg til mappen ved å:
1. Gå til "Denne PCen"
2. Legg til nettverksplassering
3. "Neste"
4. "Velg en egendefinert nettverksplasering"
5. Skrev inn "\\192.168.1.10\Prosjektmappeuke43-44", 192.168.1.10 er som nevnt IPen til serveren og Prosjektmappeuke43-44 er navnet på den delte mappen.
Med det var mappen delt og for å teste at alt fungerte prøvde jeg først å lage et tekst dokument på serveren i mappen som jeg ga navnet "Test", og sjekket at det dukket opp på klient PCen så lagde jeg et nytt tekts dokument på klient PCen som jeg ga navnet "test2" til på klient PCen. De begge dukket opp for begge. Så alt skal fungere.
<img width="680" height="283" alt="image4" src="https://github.com/user-attachments/assets/8ee4954e-64b4-4cc6-81bf-0e80b34c713f" />

Så tenker jeg å sjekke hvilke porter som er åpne på serveren, for det åpner jeg zenmap på klient PCen og scanner etter IPen til serveren, 192.168.1.10.
Zenmap er har et grafisk brukergrensesnitt så jeg fåretrekker å bruke det for Nmap, men du kan bruke terminalen også.
Der kommer det opp en lang liste med åpne porter, jeg velger bevist å ikke ta bilde av det siden det er kan vise sårbarheter i serveren.
En av portene som har "State" "Open" som betyr at den er åpen er port 53, den har "Service" "Domain" og har med DNS å gjøre. Det er det som oversetter vg.no til IP adressen x.x.x.x som er VG sin egentlige adresse.
En annen port som var åpen var 443, den behandler https sider.

Så ville jeg teste litt og se på nettverks trafikken. Det gjør jeg i wireshark. Der gikk jeg under "Ethernet" fordi det er den koblingen klient PCen er koblet til nettverket. Der starter jeg en fangst, pinget server IPen i terminalen, stoppet fangsten. Der etter filtrerer jeg resultatet etter icmp (internett controll message protocol). Da kom det at jeg pinget serveren tydelig fram, det så slikt ut:
<img width="959" height="167" alt="image5" src="https://github.com/user-attachments/assets/9d79dc16-1146-421b-9b7b-26958258d440" />
Der ser du tydelig at x.x.x.100 som er klient PCen sender en "request" til x.x.x.10 som er serveren, som så serveren sender en "reply" tilbake i linjen under.

Så ønsket jeg å se på trafikken rundt den delte mappen og når jeg gjør noe i den. Så jeg startet en fangst og gjorde som følgende:
1. åpnet mappen på serveren
2. lagde en ny mappe og satte navnet til "test1234"
3. dro en fil fra prosjektmappen til den nye mappen.
4. åpnet prosjektmappen på klient PCen
5. lagde en ny mappe og satte navnet til "lager en ny mappe på klient"
6. stoppet fangsten
Hele fangsten var på 42,4 sekunder 
Jeg filtrerte så søket til ip.addr == 192.168.1.10 for å korte ned på resultatet. Jeg er ingen mester til å lese resultatet, men jeg så dette:
<img width="933" height="239" alt="Skjermbilde 2026-10-22 112526" src="https://github.com/user-attachments/assets/628a90bb-14b3-4fbe-be51-fdd918ffe1e7" />
og med tanke på time var 22,8-28,3 og protocol var SMB2 som har med fildeling tror jeg det var det som var at jeg lagde mappen på klient PC-en og at dette var at jeg satte navn på den:
<img width="944" height="383" alt="Skjermbilde 2026-10-22 11330511" src="https://github.com/user-attachments/assets/e431f68d-4a61-4489-b395-5f47d0cfae00" />
For å klare å ta bilde av alt måtte jeg zoome ut ganske langt, men med tanke på at jeg lagde navnet på mappen langt og brukte endel tid på å skrive det tror jeg at det passer med tiden.
Jeg klarte ikke å finne noe spesifikt som viser til det jeg endret i mappen fra serveren, men min hypotese er at det var fordi jeg ikke hadde mappen åpen på klient PCen da jeg gjorde endringene. Prøvde nå å lage en mappe i den delte mappen ga den et navn og dro en fil inn, samtidig som mappen var åpen på klient, men så ikke noe som var like tydelig som det jeg gjorde på den første fangsten.

Så med å teste Porter med Nmap og teste trafikken med wireshark føler jeg meg litt mer sikker på hva det gjør

Så tok jeg en tracert 192.168.1.10 for å finne ruten fra klient til server. så en tracert 192.168.1.1 for å finne ruten til ruteren. Resultatet var sånn:
<img width="430" height="255" alt="image7" src="https://github.com/user-attachments/assets/185bcf1f-5a7c-4d46-a77b-309c5b0fc603" />
de begge ble nådd etter et hop.


