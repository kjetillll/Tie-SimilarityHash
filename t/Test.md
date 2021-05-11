
# NVB-kontrollmotor – teknisk brukerdokumentasjon

Unit.no, 11. mai 2021

## Formål med dokumentet

Formålet med dokumentet er å beskrive formatet til input og output til NVBs kontrollmotor-API og
dennes kjøreopsjoner/-moduser.

Når vitnemål og kompetansebevis skal sendes til NVB brukes samme filformat som input her. Det kan godt være de samme filene når de er klare for innsending.

De viktigste kapitlene for de som vil komme fort i gang:

• Kapittel 4 side 4: Kommandolinjeopsjoner for hvordan kontroll.exe kjøres.
• kapittel 5 side 9 og kapittel 6 side 11: Filformatet for inputfilen
• kapittel 5 side 9 og kapittel 7 side 25: Filformat for outputfilene.

## Endringer i dette dokumentet

Ingen så langt.

## Noen begreper

<table>
<tr><td>Begrep</td><td>Kort forklaring</td></tr>
<tr><td>Vgdok</td><td>NVB tok tidligere i mot kun dokumenter at type vitnemål, men kan nå også motta kompetansebevis. Vgdok (dokumentasjon fra videregående opplæring) er fellesbenevnelsen f\
or vitnemål, kompetansebevis og ev andre senere dokumenttyper fra videregående opplæring til NVB</td></tr>
<tr><td>Reform, KL, R94</td><td>Et vitnemål tilhører en reform. F.o.m. 2009 tilhører nye vitnemål KL (kunnskapsløftet), før det f.o.m. ca 1997 tilhører de R94 (Reform94).</td></tr>
<tr><td>Vgdoknr, vmnr</td><td>Identifikatoren på dokumentene. Kalles av og til vmnr eller vitnemålsnr.</td></tr>
<tr><td>Programområde</td><td>KL-begrep. Ble i Reform94 kalt kurs.</td></tr>
<tr><td>Promrkode</td><td>Programområdekode. Ble i Reform94 kalt kurs/kurskode.</td></tr>
<tr><td>Løp</td><td>Hvert vitnemål har normalt tre programområdekoder, en for hvert år. Kombinasjonen av disse (normalt tre) lagt etterhverandre kalles løp. I Reform94 kalt linje(?)</td></t\
r>
<tr><td>Utdanningsprogram</td><td>Hvert programområde ligger innen et utdanningsprogram. Utdanningsprogram ble i R94 kalt studieretning.</td></tr>
<tr><td>Nivå - VG1, VG2, VG3</td><td>Nivået angir år på videregående i KL (kunnskapsløftet). Ble i R94 kalt hhv GK (grunnkurs), VK1 og VK2.</td></tr>
<tr><td>Fellesfag (FF)</td><td>Fagene på et KL-vitnemål grupperes i fellesfag (FF), felles programfag (FPF), valgfrie programfag (VP). Fellesfag ble i R94 kalt felles almenne fag og felles \
programfag ble kalt studieretningsfag.</td></tr>
<tr><td>Omfang</td><td>Tall på vitnemål som finnes både pr fag og summert opp til et samlet tall. Angis i årstimer på KL-vitnemål og uketimer på R94. En omtrentlig omregning er å multiplise\
re med ca 28.</td></tr>
<tr><td>Melding (output fra kontrollene)</td><td>Meldinger i output-filene fra kontroll.exe inkluderer feilmeldinger (som gjør at vitnemål avvises fra NVB), varsler (som vitnemålsutsteder b\
ør lese og vurdere om noe skal rettes) og infomeldinger som oftest er uviktige. Mer om meldingene i kap 7.3 side 27.</td></tr>
<tr><td>Meldignskode</td><td>F.eks. KM101. Alle starter på KM (kontrollmelding). Meldingene fra kontroll.exe er kodet for å kunne referere til mer dokumentasjon om meldingen og å ha noe dat\
alesbart selv om meldingsteksten endres over tid i senere versjoner.</td></tr>
<tr><td>Meldingstekst<br>Meldingsparametere</td><td>En fast tekst. Ofte med plassavholdere til variable meldingsparametere.</td></tr>
<tr><td>Alvorlighetsgrad</td><td>Et tall på en melding. Tallet avgjør om meldingen ansees som feil, varsel eller info.</td></tr>
<tr><td></td><td></td></tr>
</table>
## Hvordan kjøre kontroll av vitnemål

### Kjøreopsjoner for kontroll

    -u           Angir at kontrollene skal kjøres uten kontroll av karakterføringen.
                 Nyttig for å kontrollere fagsammensetningen til en elev før han/hun
                 foretar valg av fag og lignende.

    -x           Normalt kjøres ikke fagkontrollene dersom det finnes meldinger av type FEIL i
                 filkontrollene på et vitnemål. Med -x kjøres de likevel.

    -v           Verbost modus hvor det skrives flere varsler fra filkontrollen.

    -l n         Setter loggenivået i outputfilen til tallet n. Gyldige verdier er 0-11 (default: 5) og høyere nivå gir flere meldinger.

    -d liste     Kommaseparert liste av vgdoknr som spesifiserer hvilke dokumenter som skal kontrolleres (default: alle).

    -k kravkode  Hvilket krav man kjører fagkontroller mot (default: Sammensatt av programområdene i dokumentet.).

    -T           Test-modus. Foreløpig betyr den ikke annet enn at den godtar at Vgdoknr
                 starter på T, istedenfor V eller K, og at løpenr (fire siste tegn) kan bestå av store
                 bokstaver A-Å i tillegg til vanlige sifre 0-9.

    -H           I tillegg til den vanlige maskinlesbare outputen får man med -H også en html-rapport
                 som en fil nr 2. Filene leveres som én output pakket i en TAR-fil.


Kjøreopsjonene angis i http-header `X-NVB-KM`. Det skal kun finnes en slik header pr kall. Eksempler:

     X-NVB-KM: -u
     X-NVB-KM: -k FFMAT -l 3

I første eksempel kjøres kontrollene uten karakterkontroll.

I andre eksempel kjøres kontrollen/beregningen FFMAT istedenfor default-kontrollen for løpet. Loggenivået er 3 og mindre blir logget som `¤L`-linjer i output enn i defaulten som er 5.

### Kjøreeksempler med `curl`

Her antas at input på soltegnformatet ligger i filen vm.nvb og at output skrives til resultat.nvb

    1. curl -d@vm.nvb -si -X POST https://xyz.unit.no/nvb/kontroll/v1/ > resultat.nvb
    2. curl -d@vm.nvb -si -X POST -H"X-NVB-KM: -u" https://xyz.unit.no/nvb/kontroll/v1/ > resultat.nvb
    3. curl -d@vm.nvb -si -X POST -H"X-NVB-KM: -u -x" https://xyz.unit.no/nvb/kontroll/v1/ > resultat.nvb
    4. curl -d@vm.nvb -si -X POST -H"X-NVB-KM: -k GSK" https://xyz.unit.no/nvb/kontroll/v1/ > resultat.nvb
    5. curl -d@vm.nvb -si -X POST -H"X-NVB-KM: -H" https://xyz.unit.no/nvb/kontroll/v1/ > resultat.tar

Forklaring:

    1. Sender innhold i vm.nvb til endepunktet (angitt url), kjører default kontroller på default loggenivå (dvs 5) og legger output i filen resultat.nvb
    2. Nå uten karakterkontroller
    3. Nå kjøres fagkontrollene selv om det skulle finnes feil i filkontrollene
    4. Nå kjøres kontrollen GSK som gir SANN|USANN på om vitnemålet kan gi generell studiekompetanse i Samordna opptak til høyere utdanning
    5. Som 1, men leverer i tillegg en html-rapport fra kjøringen

### Loggenivå

<table>
<tr><td>**Nivå**</td><td>**Hva mer logges i forhold til forrige nivå**</td></tr>
<tr><td>0</td><td>Logger ikke noe. Ingen ¤L-linjer på datafilen/resultatfilen.</td></tr>
<tr><td>1</td><td>Logger bare systemfeil.</td></tr>
<tr><td>2</td><td>Logger alle USANN-meldinger frem t.o.m. den meldingen som evt. viser hvorfor kontrollen
avbrytes og vitnemålet forkastes. Dette vil normalt være meldinger om FEIL, men kan også
være VARSLER. Eks: ”Mangler felles allmenne fag”. Grupperer meldingene for hvert
vitnemålsnummer. Se kravmeldingtabell www.samordnaopptak.no/nvb/vmkrav.input
.txt.html for fullstendig oversikt over USANN-meldinger.</td></tr>
<tr><td>3</td><td>Logger også alle SANN-meldinger frem til og med den meldingen som evt. viser hvorfor
vitnemålet forkastes. Eks: ”Krav til omfang for studieretningsfag oppfylt”. Skriver også
hovedoverskrifter for kontrollene, eks: ”KONTROLL AV FELLES ALLMENNE FAG”. Se
kravmeldingstabell i www.samordnaopptak.no/nvb/vmkrav.input.txt.html for fullstendig
oversikt over SANN-meldinger.</td></tr>
<tr><td>4</td><td>Logger hvilke fag som er "oppbrukt", altså hvilke fagkoder som har gått med til å tilfredsstille
kravene under de ulike hovedkontrollene. Og gir FAGLOGG-meldinger, se
www.samordnaopptak.no/nvb/vmkrav.input.txt.html for en fullstendig oversikt over slike.</td></tr>
<tr><td>5</td><td>Gir en sluttrapport som viser vitnemålsmerknader, alle fag som er brukt i kontrollene, fag på
vitnemålet som ikke ble brukt for å tilfredsstille kontroller, og totalomfang, antall karakterer,
sum karakterer og karaktersnitt. Standardnivå for logger i den sentrale NVB-basen.</td></tr>
<tr><td>6</td><td>Lager en sluttrapport for hver hovedkontroll.</td></tr>
<tr><td>7</td><td>Logger hver enkelt kravuttrykkrad i det man starter kontroll av den. Se
www.samordnaopptak.no/nvb/vmkrav.input.txt.html for en fullstendig oversikt over alle
kravuttrykkrader. Nivå 7 og utover er mest for teknisk debugging.</td></tr>
<tr><td>8</td><td>Logger operander med resultat som gir SANN, se
www.samordnaopptak.no/nvb/vmkrav.input.txt.html for fullstendig oversikt over operander
(gitt ved kravuttrykknr) og kjøring av ikke_oppbrukt(). ??????</td></tr>
<tr><td>9</td><td>Logger alle operander uansett resultat.</td></tr>
<tr><td>10</td><td>Viser også hvilke kravuttrykk som hoppes over, fordi resultatet allerede er gitt ved kontroll av
andre kravuttrykk</td></tr>
<tr><td>11-</td><td>F.o.m. 11: udefinert/udokumentert</td></tr>
</table>

## Filformat

Filformatet er linjeinndelte rader (records) med ¤ som skille mellom hver verdi (felt). Tegn nummer 2
på hver linje angir hvilken tabell linjens data gjelder.

### Tegnsett

UTF-8 er tegnsettet som benyttes. I dette tegnsettet kan hvert tegn
bestå av mer enn en byte, feks består ¤ av to bytes: 194 og 164 (C2 og
A4 i hex). Æ, Ø, Å og flere andre består også av 2-4 tegn. (På
forespørsel kan det i en overgangsperiode vurderes å støtte det gamle
tegnsettet ISO-8859-1 som ble brukt i kontroll.exe i forrige utgave av
NVB).

### Skilletegn

Både input- og output-filer har fast feltskilletegn ¤. Dette ”soltegnet” er lite brukt ellers og lett å finne
på norske Windows-tastaturer: shift-4. I tillegg til å skille hvert felt skal ¤ stå først på hver linje
(unntak på side 10), men ikke sist med mindre siste felt har blank verdi.

### Linjetypene i inputfilen

<table>
<tr><td>¤A</td><td>–</td><td>Startlinjen for hver skole/orgnr. Det kan være flere ¤A i samme fil.</td></tr>
<tr><td>¤S</td><td>Orgnr</td><td>Frivillige tilleggsopplysninger til ¤A om denne skolen / dette orgnummeret</td></tr>
<tr><td>¤V</td><td>Vgdoknr</td><td>Vgdok-linje, dokumenthodet, vitnemålshodet - opplysninger det bare finnes en av på hvert vitnemål</td></tr>
<tr><td>¤P</td><td>Vgdoknr og Promrkode</td><td>Vgdokpromr-linje, en linje for hvert programområde på dokumentet</td></tr>
<tr><td>¤F</td><td>Vgdoknr og Fagkode</td><td>Vgdokfag-linje, en linje for hvert fag på dokumentet</td></tr>
<tr><td>¤M</td><td>Vgdoknr og Merknadnr</td><td>Vgdokmerknader, en linje for hver vitnemålsmerknad (eller kompetansebevismerknad)</td></tr>
<tr><td>¤D</td><td>Vgdoknr</td><td>Vgdokannullering-linje</td></tr>
</table>

### Linjetypene i resultatfilen

<table>
<tr><td>¤R</td><td>–</td><td>Startlinjen, kun en pr fil, første linje</td></tr>
<tr><td>¤K</td><td>Kontrollnr</td><td>Kontrollresultat, en linje pr kontroll kjørt. Default en ¤K-linje pr vitnemål i inputfilen</td></tr>
<tr><td>¤E</td><td>Meldingsnr</td><td>Meldingslinje (info-, tips-, varsel- eller feilmelding)</td></tr>
<tr><td>¤L</td><td>Kontrollnr + Linjenr</td><td>Logglinje</td></tr>
</table>

### Feltformat

I kap. 6 brukes følgende koding for å beskrive feltene:

<table>
    <tr><td>N</td><td>Heltall uten grense for maks antall sifre. Kan være 0, men ikke negativt</td></tr>
    <tr><td>Nx</td><td>Heltall med maksimalt x sifre. Kan være 0, men ikke negativt</td></tr>
    <tr><td>N0x</td><td>Heltall med x sifre der ledende nuller brukes. F.eks. er postnr typisk N04 for å ikke miste nullen foran på postnumre i Oslo</td></tr>
    <tr><td>Nx.y</td><td>Desimaltall. Den maksimale feltbredden er x tegn, inkl punktumet, og feltet kan ha opptil y desimaler. Eksempel: Et N7.5-felt kan inneholde 3.14159, men ikke 36.46195 eller 3.141592</td></tr>
    <tr><td>A</td><td>Alfanumerisk felt. Se avsnitt 5.7 og 5.8 for behandling av spesialtegn</td></tr>
    <tr><td>Ax</td><td>Alfanumerisk, maksimalt x tegn</td></tr>
    <tr><td>D8</td><td>Datofelt på formen ÅÅÅÅMMDD der ÅÅÅÅ er årstall på fire sifre, MM er måned med to
sifre 01-12 og DD er dato med to sifre 01-31. Eksempel: 8. mai 1945 skrives 19450508.
Oracle-eksempel: select to_char(datefelt,'YYYYMMDD')</td></tr>
    <tr><td>K6</td><td>Klokkeslett på formen TTMMSS der timen TT er to sifre 00-23, minutt MM er to sifre 00-59
og sekundsangivelsen SS er to sifre 00-59. Ledende nuller skal være med (feks er 8:49 *ikke* akseptert når det skal stå 084900). Eksempel: fem over åtte på kvelden skrives 200500. Oracle-eksempel: select to_char(datefelt,'HH24MISS')</td></tr>
    <tr><td>T14</td><td>D8 og K6 sammenslått i ett tidspunkt-felt</td></tr>
</table>    
