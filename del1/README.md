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

Dersom jeg skulle trenge å dokumentere noe fra serveren tenkte jeg det var smart å sette opp en delt mappe så jeg lett kunne overføre bilder mellom maskinene. Mappen lagde jeg bare på skrivebordet til serveren, slo på shareing og endre på security og ga tilgang til alle til å legge til, endre og fjerne filer i mappen.
Så på klient pcen koblet jeg meg til mappen ved å:
1. Gå til "Denne PCen"
2. Legg til nettverksplassering
3. "Neste"
4. "Velg en egendefinert nettverksplasering"
5. Skrev inn "\\192.168.1.10\Prosjektmappeuke43-44", 192.168.1.10 er som nevnt IPen til serveren og Prosjektmappeuke43-44 er navnet på den delte mappen.
Med det var mappen delt og for å teste at alt fungerte prøvde jeg først å lage et tekst dokument på serveren i mappen som jeg ga navnet "Test", og sjekket at det dukket opp på klient PCen så lagde jeg et nytt tekts dokument som jeg ga navnet "test2" til på klient PCen. De begge dukket opp for begge. Så alt skal fungere.
<img width="680" height="283" alt="image4" src="https://github.com/user-attachments/assets/8ee4954e-64b4-4cc6-81bf-0e80b34c713f" />
