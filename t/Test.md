
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

I kolonnene **Oblig.** står det Ja hvis feltet er obligatorisk. Det kan da ikke være tomt/blankt.

### Feltbredde
Selv om filformatet bruker skilletegn ¤ og slik sett ikke har fast bredde, er det likevel hensiktsmessig å
definere en maksimumslengde på mange av feltene. Bl.a. fordi dataene ender opp i skjermtabeller som ikke
nødvendigvis har uendelig plass i bredden og i databasefelt med definerte maxbredder.
Inputdata som overskrider feltbredden angitt her vil medføre en varselmelding (¤E) fra valideringen og
feltene vil avkuttes internt i kontrollmotoren / før import til NVB. At meldingen er av type varsel (og ikke feil) betyr at kjøringen
ikke avbrytes pga for lange felt.

### Linjeskift
Linjeskift er binært byte 10 (hex-A) eller byte 13 (hex-D) eller flere påfølgende tegn av en eller begge
av disse. Dette for at kontroll.exe skal tåle normal output uansett om filen kommer fra Windows, Mac,
Linux, databasen el.l. Kontroll.exe hopper over tomme linjer (inkl. linjer med kun space og tab).

### Skilletegnet ¤ i dataene
Selv om skilletegnet ¤ er sjelden brukt er det likevel mulig at sluttbrukere skriver det inn i fritekstfelter.
Et felt som inneholder ¤ i selve teksten omhylles med { og } som første og siste tegn i feltet for å
beskytte skilletegnet. Dersom { eller } står inne i et felt behandles de som vanlige tegn. For å slippe å
ta hensyn til dette kan man godt bare avgjøre at brukerinput aldri har behov for tegnet ¤ og automatisk
erstatte det med f.eks. * når man lager fil til kontroll.exe og NVB, f.eks. vha funksjonen
replace(felt,'¤','*') i Oracle.
Kontroll.exe vil "trimme" feltverdiene for mellomrom og tab-tegn i starten eller slutten av feltet og det
anbefales at det samme skjer for innlesing av resultatfilen.

### Linjeskift i dataene
For felter som inneholder linjeskift i selve dataene er det to alternativer:
1. Feltet omhylles med { og } som første og siste tegn i feltet. Dermed vil det kunne finnes linjer i inputfilen som ikke starter med ¤.
2. Eller linjeskift angis med de to tegnene \n Alternativ 2 anbefales og kan ordnes med f.eks. dette i Oracle: `select replace(felt,chr(10),'\n')` eller 
`select replace(replace(felt,chr(10),'\n'),chr(13),'\n')`

### Filformat JSON
Mulighet for input- og outputfiler på JSON kommer kanskje i en senere versjon.

### Tegnsett
Valideringen foretrekker tegnsettet UTF-8. Dette har tatt over for ISO-8859-1 som ble bruke i forrige utgave av NVB.

## Linjetyper og felter i inputfilen
Feltnavnene her angir hva feltene heter i NVBs database. Hva de heter hos systemleverandørene er
deres valg.

Primærnøkkel er angitt med understreket feltnavn i tabellene under

### Startlinjer ¤A
I hver fil skal det være en ¤A for hvert orgnr i ¤V- og ¤D-linjene. Det skal altså ikke forekomme orgnr
i ¤V og ¤D uten en ¤A.
¤V- og ¤D-linjer skal stå under ¤A-en de tilhører (samme orgnr). Alle ¤A-linjene skal altså ikke
samles i toppen av filen (mer om dette i avsnitt 6.8 Rekkefølgen av linjetyper side 24)

<table>
	<tr>
		<td><b>Feltnr</b></td>
		<td><b>Feltnavn</b></td>
		<td><b>Oblig.</b></td>
		<td><b>Format</b></td>
		<td><b>Eksempel</b></td>
		<td><b>Forklaring</b></td>
	</tr>
	<tr>
		<td>A0</td>
		<td>Linjetype</td>
		<td>Ja</td>
		<td>A2</td>
		<td>¤A</td>
		<td>Alltid ¤A</td>
	</tr>
	<tr>
		<td>A1</td>
		<td>Orgnr</td>
		<td>Ja</td>
		<td>N9</td>
		<td>979958986</td>
		<td>Organisasjonsnr. Skal finnes i nasjonalt skoleregister. SO (og andre) kan gjøre et nytt forsøk på å innarbeide NSR i NVB. Orgnr skal stå i Foretaksregisteret (på<br>www.brreg.no ). Se side 30 for utenlandske skoler uten norsk organisasjonsnummer.</td>
	</tr>
	<tr>
		<td>A2</td>
		<td>Skolenr</td>
		<td>Ja</td>
		<td>N05</td>
		<td>1020</td>
		<td>Skolens VIGO-nummer. De to første sifrene er fylkesnr for fylkeskommunale skoler og 00 for privatskoler. NB: Skolens VIGO-nummer må ikke forveksles med RVO-nr og andre femsifrede skolenummer som har eksistert.</td>
	</tr>
	<tr>
		<td>A3</td>
		<td>Antall_vgdok</td>
		<td>Ja</td>
		<td>N</td>
		<td>123</td>
		<td>Antall ¤V i denne filen med samme orgnr som A1</td>
	</tr>
	<tr>
		<td>A4</td>
		<td>Antall_vgdokann</td>
		<td>Ja</td>
		<td>N</td>
		<td>0</td>
		<td>Antall ¤D i denne filen med samme orgnr som A1.</td>
	</tr>
	<tr>
		<td>A5</td>
		<td>Antall_vgdokfag</td>
		<td>Ja</td>
		<td>N</td>
		<td>2345</td>
		<td>Antall ¤F i denne filen som som tilhører ¤V med samme orgnr som A1.</td>
	</tr>
	<tr>
		<td>A6</td>
		<td>Antall_vgdokpromr</td>
		<td>Ja</td>
		<td>N</td>
		<td>345</td>
		<td>Antall ¤P i denne filen som tilhører ¤V med samme orgnr som A1</td>
	</tr>
	<tr>
		<td>A7</td>
		<td>Antall_vgdokmerknad</td>
		<td>Ja</td>
		<td>N</td>
		<td>12</td>
		<td>Antall ¤M i denne filen som tilhører ¤V med samme orgnr som A1</td>
	</tr>
	<tr>
		<td>A8</td>
		<td>Systemnavn</td>
		<td>Ja</td>
		<td>A</td>
		<td>Systemnavn</td>
		<td>Navnet på systemet som har laget filen. (Med kjøreopsjon -V må det stå Vigo her).</td>
	</tr>
	<tr>
		<td>A9</td>
		<td>Systemversjon</td>
		<td>Ja</td>
		<td>A</td>
		<td>5.0.1</td>
		<td>Versjonsnummer som lar seg sammenligne med tidligere versjonsnumre slik at man ved alfanumerisk sortering kan avgjøre og varsle brukere som har en lavere versjon enn andre.</td>
	</tr>
	<tr>
		<td>A10</td>
		<td>Tid_fil_laget</td>
		<td>Ja</td>
		<td>T14</td>
		<td>20210512090100</td>
		<td>Tidspunkt for når filen ble laget. Norsk tid.</td>
	</tr>
</table>

### Skoleinfolinjer ¤S
Hver ¤A-linje kan følges av en ¤S-linje med skoleinformasjon. SO bruker dataene i ¤S-linjene til
vedlikehold av sitt lokale skoleregister (med ujevne mellomrom, ingen automatikk). Det er frivillig om
¤S alltid sendes etter ¤A, eller kun når endringer har skjedd.

<table>
<tr><th>Feltnr</th><th>Feltnavn</th><th>Oblig.</th><th>Format</th><th>Eksempel</th><th>Forklaring</th></tr>
<tr><td>S0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤S</td><td>Alltid ¤S</td></tr>
<tr><td>S1</td><td>Orgnr</td><td>Ja</td><td>N9</td><td style="text-align: right">979958986</td><td>Organisasjonsnr. Skal finnes i NVBs</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>skoleregister og i Foretaksregisteret (på</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>www.brreg.no )</td></tr>
<tr><td>S2</td><td>Orgnr_gml</td><td></td><td>N9</td><td style="text-align: right">01020</td><td>Tidligere orgnr. Feltet brukes ikke av<br/>programmer, men kan være til hjelp for<br/>å nøste opp i endringer av navn og nr i<br/>NVB.</td></tr>
<tr><td>S3</td><td>Skolenr</td><td>Ja</td><td>N05</td><td style="text-align: right">00123</td><td>Skolens VIGO-nummer. Se A2 side 12.</td></tr>
<tr><td>S4</td><td>Skolenr_gml</td><td></td><td>N05</td><td></td><td>Tidligere VIGO-skolenr.</td></tr>
<tr><td>S5</td><td>Orgnavn</td><td></td><td>A</td><td></td><td>Organisasjonens navn slik det er i<br/>Enhetsregisteret (www.brreg.no)</td></tr>
<tr><td>S6</td><td>Skolenavn</td><td>Ja</td><td>A</td><td>Ås<br/>videregå-<br/>ende skole</td><td>Skolens fulle navn slik det er når filen<br/>lages</td></tr>
<tr><td>S7</td><td>Fylkesnr</td><td>Ja</td><td>N2</td><td style="text-align: right">03</td><td>Fylket skolen ligger i. Oftest samme<br/>som de to første sifrene i S3, unntatt for<br/>privatskoler. Skoler i utlandet kan bruke<br/>fylkesnr 24 og kommunetall 0.</td></tr>
<tr><td>S8</td><td>Kommunetall</td><td>Ja</td><td>N2</td><td style="text-align: right">01</td><td>Skolens kommunetall. S7+S8 utgjør</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>tilsammen et gyldig norsk firesifret</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kommunenr slik de er definert av SSB.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Dersom «skolen» er et eksamenskontor</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>el.l., bruk kommunen der kontoret er.</td></tr>
<tr><td>S9</td><td>Bydelsnavn</td><td></td><td>A</td><td></td><td>Frivillig. Bydel for de største byene som</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>har offisielle bydelsadministrasjoner.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Bruk navnet her siden inndeling og</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>nummerering stadig endres. Bruk kun</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>bydelsnr dersom navnet er ukjent. (SO</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>har blitt spurt om bydelsstatistikk før...)</td></tr>
<tr><td>S10</td><td>Skoletype</td><td>Ja</td><td>A1</td><td>F</td><td>F = fylkeskommunal (de fleste v.g.s.)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>P = privat</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>K= kommunal</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>S = statlig</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>U = utenlandsk (se side 30 for skoler</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>med manglende orgnr)</td></tr>
<tr><td>S11</td><td>Eksamensrett</td><td></td><td>A1</td><td>J</td><td>J eller ingenting. Om skolen har<br/>eksamensrett</td></tr>
<tr><td>S12</td><td>Orgnr_eier</td><td></td><td>N9</td><td></td><td>Dersom orgnr i S1 er eid eller er en<br/>filial av et annet orgnr. S12 er oftest<br/>mest aktuelt for private skoler.</td></tr>
<tr><td>S13</td><td>Kontaktperson</td><td>Ja</td><td>A</td><td>Donald<br/>Duck</td><td>Navn på kontaktperson for NVB på<br/>skolen</td></tr>
<tr><td>S14</td><td>Kontaktperson_tittel</td><td></td><td>A</td><td>Rektor</td><td>Vedkommendes rolle på skolen, f.eks.<br/>rektor, inspektør, sekretær etc</td></tr>
<tr><td>S15</td><td>Kontaktperson_epost</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>E-postadresse til kontaktpersonen</td></tr>
<tr><td>S16</td><td>Kontaktperson_tlf</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>Telefonnr til kontaktperson.</td></tr>
<tr><td>S17</td><td>Kontaktperson2</td><td></td><td>A</td><td></td><td>Tilsvarende S13. Annen person.</td></tr>
<tr><td>S18</td><td>Kontaktperson2_tittel</td><td></td><td>A</td><td></td><td>Tilsvarende S14</td></tr>
<tr><td>S19</td><td>Kontaktperson2_epost</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>Tilsvarende S15</td></tr>
<tr><td>S20</td><td>Kontaktperson2_tlf</td><td></td><td>A</td><td></td><td>Tilsvarende S16</td></tr>
<tr><td>S21</td><td>Adrlinje1</td><td></td><td>A</td><td></td><td>Skolens postadresse</td></tr>
<tr><td>S22</td><td>Adrlinje2</td><td></td><td></td><td></td><td>Skolens postadresse</td></tr>
<tr><td>S23</td><td>Adrpostnr</td><td></td><td>N04</td><td style="text-align: right">6440</td><td>Skolens postadresse, postnr. Skal være</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>utfylt for norske adresser. Skal ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>være utfylt dersom S25 er utfylt. Skal</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kun inneholde norske postnr,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>utenlandske postnr flyttes over til S24.</td></tr>
<tr><td style="text-align: right" colspan="6">13</td></tr>
<tr><td>S24</td><td>Adrpoststed</td><td>Ja</td><td>A</td><td>Bud</td><td colspan="2">Skolens postadresse: land, helst på</td></tr>
<tr><td>S25</td><td>Adrlandnavn</td><td></td><td>A</td><td></td><td colspan="2">Skolens postadresse: land, helst på</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">engelsk. Oppgi blank verdi for norske</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">adresser. (Norge, Noreg, Norway o.l. er</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">unødvendig). S23 er alltid blank hvis</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">S25 er utfylt og omvendt.</td></tr>
<tr><td>S26</td><td>Adrbesoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, gatenavn+nr<br/>eller sted. Unødvendig å fylle ut S26-<br/>S29 om besøks- og postadresse er like.</td></tr>
<tr><td>S27</td><td>Adrpostnr_besoek</td><td></td><td>N04</td><td></td><td colspan="2">Skolens besøksadresse, postnr. Norsk<br/>postnr hvis utfylt. Utenlandske postnr<br/>flyttes over til S28.</td></tr>
<tr><td>S28</td><td>Adrpoststed_besoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, poststed.</td></tr>
<tr><td>S29</td><td>Adrlandnavn_besoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, land. S27 skal<br/>ikke være utfylt hvis S29 er det og<br/>omvendt.</td></tr>
<tr><td>S30</td><td>Telefonnr</td><td>Ja</td><td>A</td><td style="text-align: right">71232100</td><td colspan="2">Skolens telefonnr</td></tr>
<tr><td>S31</td><td>Telefaksnr</td><td></td><td>A</td><td style="text-align: right">71232101</td><td colspan="2">Skolens telefaksnr</td></tr>
<tr><td>S32</td><td>Epost</td><td></td><td>A</td><td>post@skole.<br/>no</td><td colspan="2">Skolens epostadresse, bør være utfylt<br/>dersom S15 og S19 er blanke. Flere<br/>adresse adskilles med , (komma)</td></tr>
<tr><td>S33</td><td>Webadresse</td><td></td><td>A</td><td>www.skole.<br/>no</td><td colspan="2">Skolens hjemmeside på internettet ...<br/>http:// er unødvendig</td></tr>
</table>
