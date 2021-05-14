
# NVB-kontrollmotor – teknisk brukerdokumentasjon for validering av vitnemål

Unit.no, 11. mai 2021

## Formål med dokumentet

Formålet med dokumentet er å beskrive filformatet for input og output fra NVBs kontrollmotor-API og
dennes kjøreopsjoner og moduser.

Når vitnemål skal registreres/importeres i NVB brukes samme filformat som for kontroll/validering. Det kan m.a.o. være samme fil som først kontrolleres og så sendes inn når vitnemålene utstedes.

De viktigste kapitlene for å komme fort i gang:

* Kapittel 4 side 4: Kommandolinjeopsjoner for hvordan kontroll.exe kjøres.
* kapittel 5 side 9 og kapittel 6 side 11: Filformatet for inputfilen
* kapittel 5 side 9 og kapittel 7 side 25: Filformat for outputfilene.

## Endringer i dette dokumentet

Ingen så langt.

## Liste over begreper brukt her

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

## Filformat for input og output

Filformatet er linjeinndelt. En linje er en record med ¤ som skille mellom hvert felt. Tegnet ¤ ble valgt fordi det er sjeldent brukt i tekst,
men likevel lett å skrive på norske PC-tastaturer med shift-4.

Første tegn på hver linje er ¤ 

Tegn nr 2 på hver linje er en stor bokstav som angir record typen. Bokstavkodene er A S V P F M D R L K eller E.
Bokstaven koder hvilken tabell linjens data gjelder om man tenker på dette som databasetabeller.

Siste tegn på hver linje er linjeskift. Normalt ascii-10-tegnet alene. (Dessverre har ikke verden vært helt enig historisk om hvordan linjeskift
skal kodes. Derfor godtar APIet også de to variantene med to-tegnskombinasjonen ascii-13 fulgt av ascii-10 samt ascii-13 alene som linjeskift.
Disse har historisk vært, og er det trolig fortsatt, mye brukt på hhv PC/Windows og Mac).

Formatet kan sies å være [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) der ¤ erstatter komma eller semikolon for å skille feltene.
Historisk har CSV stått for _comma-separated values_, men _character-separated values_ har vært foreslått som et bedre og mer inkluderende navn
som også dekker bruk av andre skilletegn. Dette er normalt et enkelt filformat å både skrive og lese (parse) i programmer, men man må passe på
at tekstfelt kan inneholde både ¤ og linjeskift i seg selv og at disse må behandles riktig (mer om dette under).

### Tegnsett

[UTF-8](https://en.wikipedia.org/wiki/UTF-8) er tegnsettet som benyttes. (Internetts mest brukte og i 2021 defaulten i mange systemer og
programmeringsspråk. I en overgangsperiode kan [ISO-8859-1](https://en.wikipedia.org/wiki/ISO-8859-1) vurderes støttet. På forespørsel.
Dette er default tegnsett i kontroll.exe i forrige utgave av NVB, og var det mest brukte tegnsettet i den vestlige verden dengang).

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
<tr><td><b>Feltnr</b></td><td><b>Feltnavn</b></td><td><b>Oblig.</b></td><td><b>Format</b></td><td><b>Eksempel</b></td><td><b>Forklaring</b></td></tr>
<tr><td>S0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤S</td><td>Alltid ¤S</td></tr>
<tr><td>S1</td><td>Orgnr</td><td>Ja</td><td>N9</td><td style="text-align: right">979958986</td><td>Organisasjonsnr. Skal finnes i NVBs skoleregister og i Foretaksregisteret (på www.brreg.no)</td></tr>
<tr><td>S2</td><td>Orgnr_gml</td><td></td><td>N9</td><td style="text-align: right">01020</td><td>Tidligere orgnr. Feltet brukes ikke av programmer, men kan være til hjelp for å nøste opp i endringer av navn og nr i NVB.</td></tr>
<tr><td>S3</td><td>Skolenr</td><td>Ja</td><td>N05</td><td style="text-align: right">00123</td><td>Skolens VIGO-nummer. Se A2 side 12.</td></tr>
<tr><td>S4</td><td>Skolenr_gml</td><td></td><td>N05</td><td></td><td>Tidligere VIGO-skolenr.</td></tr>
<tr><td>S5</td><td>Orgnavn</td><td></td><td>A</td><td></td><td>Organisasjonens navn slik det er i Enhetsregisteret (www.brreg.no)</td></tr>
<tr><td>S6</td><td>Skolenavn</td><td>Ja</td><td>A</td><td>Ås videregå- ende skole</td><td>Skolens fulle navn slik det er når filen lages</td></tr>
<tr><td>S7</td><td>Fylkesnr</td><td>Ja</td><td>N2</td><td style="text-align: right">03</td><td>Fylket skolen ligger i. Oftest samme som de to første sifrene i S3, unntatt for privatskoler. Skoler i utlandet kan bruke fylkesnr 24 og kommunetall 0.</td></tr>
<tr><td>S8</td><td>Kommunetall</td><td>Ja</td><td>N2</td><td style="text-align: right">01</td><td>Skolens kommunetall. S7+S8 utgjør tilsammen et gyldig norsk firesifret kommunenr slik de er definert av SSB. Dersom «skolen» er et eksamenskontor el.l., bruk kommunen der kontoret er.</td></tr>
<tr><td>S9</td><td>Bydelsnavn</td><td></td><td>A</td><td></td><td>Frivillig. Bydel for de største byene som har offisielle bydelsadministrasjoner. Bruk navnet her siden inndeling og nummerering stadig endres. Bruk kun bydelsnr dersom navnet er ukjent. (SO har blitt spurt om bydelsstatistikk før...)
    <tr><td>S10</td><td>Skoletype</td><td>Ja</td><td>A1</td><td>F</td>
    <td>F = fylkeskommunal (de fleste v.g.s.)<br>
	P = privat<br>
	K = kommunal<br>
	S = statlig<br>
	U = utenlandsk (se side 30 for skoler med manglende orgnr)</td></tr>
<tr><td>S11</td><td>Eksamensrett</td><td></td><td>A1</td><td>J</td><td>J eller ingenting. Om skolen har eksamensrett</td></tr>
<tr><td>S12</td><td>Orgnr_eier</td><td></td><td>N9</td><td></td><td>Dersom orgnr i S1 er eid eller er en filial av et annet orgnr. S12 er oftest mest aktuelt for private skoler.</td></tr>
<tr><td>S13</td><td>Kontaktperson</td><td>Ja</td><td>A</td><td>Donald Duck</td><td>Navn på kontaktperson for NVB på skolen</td></tr>
<tr><td>S14</td><td>Kontaktperson_tittel</td><td></td><td>A</td><td>Rektor</td><td>Vedkommendes rolle på skolen, f.eks. rektor, inspektør, sekretær etc</td></tr>
<tr><td>S15</td><td>Kontaktperson_epost</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>E-postadresse til kontaktpersonen</td></tr>
<tr><td>S16</td><td>Kontaktperson_tlf</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>Telefonnr til kontaktperson.</td></tr>
<tr><td>S17</td><td>Kontaktperson2</td><td></td><td>A</td><td></td><td>Tilsvarende S13. Annen person.</td></tr>
<tr><td>S18</td><td>Kontaktperson2_tittel</td><td></td><td>A</td><td></td><td>Tilsvarende S14</td></tr>
<tr><td>S19</td><td>Kontaktperson2_epost</td><td></td><td>A</td><td style="text-align: right">97713246</td><td>Tilsvarende S15</td></tr>
<tr><td>S20</td><td>Kontaktperson2_tlf</td><td></td><td>A</td><td></td><td>Tilsvarende S16</td></tr>
<tr><td>S21</td><td>Adrlinje1</td><td></td><td>A</td><td></td><td>Skolens postadresse</td></tr>
<tr><td>S22</td><td>Adrlinje2</td><td></td><td></td><td></td><td>Skolens postadresse</td></tr>
<tr><td>S23</td><td>Adrpostnr</td><td></td><td>N04</td><td style="text-align: right">6440</td><td>Skolens postadresse, postnr. Skal være utfylt for norske adresser. Skal ikke være utfylt dersom S25 er utfylt. Skal kun inneholde norske postnr, utenlandske postnr flyttes over til S24.</td></tr>
<tr><td>S24</td><td>Adrpoststed</td><td>Ja</td><td>A</td><td>Bud</td><td colspan="2">Skolens postadresse: land, helst på</td></tr>
<tr><td>S25</td><td>Adrlandnavn</td><td></td><td>A</td><td></td><td colspan="2">Skolens postadresse: land, helst på engelsk. Oppgi blank verdi for norske adresser. (Norge, Noreg, Norway o.l. er unødvendig). S23 er alltid blank hvis S25 er utfylt og omvendt.</td></tr>
<tr><td>S26</td><td>Adrbesoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, gatenavn+nr eller sted. Unødvendig å fylle ut S26- S29 om besøks- og postadresse er like.</td></tr>
<tr><td>S27</td><td>Adrpostnr_besoek</td><td></td><td>N04</td><td></td><td colspan="2">Skolens besøksadresse, postnr. Norsk postnr hvis utfylt. Utenlandske postnr flyttes over til S28.</td></tr>
<tr><td>S28</td><td>Adrpoststed_besoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, poststed.</td></tr>
<tr><td>S29</td><td>Adrlandnavn_besoek</td><td></td><td>A</td><td></td><td colspan="2">Skolens besøksadresse, land. S27 skal ikke være utfylt hvis S29 er det og omvendt.</td></tr>
<tr><td>S30</td><td>Telefonnr</td><td>Ja</td><td>A</td><td style="text-align: right">71232100</td><td colspan="2">Skolens telefonnr</td></tr>
<tr><td>S31</td><td>Telefaksnr</td><td></td><td>A</td><td style="text-align: right">71232101</td><td colspan="2">Skolens telefaksnr</td></tr>
<tr><td>S32</td><td>Epost</td><td></td><td>A</td><td>post@skole.no</td><td colspan="2">Skolens epostadresse, bør være utfylt dersom S15 og S19 er blanke. Flere adresse adskilles med , (komma)</td></tr>
<tr><td>S33</td><td>Webadresse</td><td></td><td>A</td><td>www.skole.no</td><td colspan="2">Skolens hjemmeside på internettet ... http:// er unødvendig</td></tr>
</table>

### 6.3 Vgdok-linjer ¤V
Spesielt om vgdoknr: Feltet Vgdoknr er unikt og skal aldri gjenbrukes dersom dokumentet er utstedt
(gitt til eleven) eller sendt inn til NVB. Hvis et vitnemål eller kompetansebevis skal endres skal det få
et nytt vgdoknr og det gamle skal annulleres (med en ¤D-linje, se side 23) selv om endringen er
minimal. SO viser fram vitnemål til søkere til høyere utdanning og må da kunne vise nøyaktig det
samme som står på orginaldokumentet på papir.

<table>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td colspan="2">Forklaring</td></tr>
<tr><td>V0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤V</td><td colspan="2">Alltid ¤V</td></tr>
<tr><td>V1</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td>V97995898620080001</td>
  <td>Vgdoknr (dokumentidentifikator).<br>
    • Første tegn er bokstaven V for vitnemål og K for kompetansebevis<br>
    • Så følger orgnr ni siffer for utstederorganisasjonen (oftest en skole).<br>
    • Deretter årstall, fire siffer.<br>
    • Og til slutt et løpenr på fire siffer 0001-9999.<br>
<br>
    Her kan også gamle vmnr stå: 13 siffer. (Ingen systemleverandører må lage nye vgdoknr på gamle vitnemål) Lov for VIGO: Store bokstaver A-Å i løpenr i tillegg til sifre.</td></tr>
<tr><td>V2</td><td>Foerstegangsvm</td><td></td><td>A1</td><td>J</td>
  <td>J = ja<br>
      N = nei<br>
      Førstegangsvitnemål eller ikke står også på vitnemålet. Primærvitnemål heter<br/>dette i R94. Kan være J selv om eleven<br/>er eldre enn 21. Feltet er viktig i opptak<br/>til høgskoler/universiteter fordi det kan<br/>gi adgang til førstegangsvitnemålskvo-<br/>tene for søkere t.o.m. 21 år. Disse utgjør<br/>ofte 50% av studieplassene.</td></tr>
<tr><td>V4</td><td>Reformkode</td><td>Ja</td><td>A3</td><td>KL</td><td>KL = Kunnskapsløftdokument<br/>R94 = Reform94-dokument<br/>Eldre reformer støttes ikke av NVB.</td></tr>
<tr><td>V5</td><td>Vgdoktypekode</td><td>Ja</td><td>A2</td><td>VM</td><td>VM = vitnemål<br/>KB = kompetansebevis</td></tr>
<tr><td>V6</td><td>Avgangsaar</td><td>Ja</td><td>N4</td><td style="text-align: right">2008</td><td>Avgangsår. Må ikke forveksles med årstallet i V9-Dato_utstedt eller innsendingsår til NVB. Årstallene i V9 og V6 kan være forskjellig. Skal normalt være likt tegnene 11-14 i felt V1. V6 settes også for kompetansebevis selv om det da ikke kan kalles avgangsår. Det året man har fullført og bestått vitnemålet. Etter de gamle reglene (som gjelder fremdeles?) skal det gamle året stå, selv om det er forbedringer. Bør presiseres....</td></tr>
<tr><td>V7</td><td>Orgnr</td><td>Ja</td><td>N9</td><td style="text-align: right">979958986</td><td>Utsteders/skolens organisasjonsnr.<br/>Normalt likt tegn 2 til 10 i V1 4</td></tr>
<tr><td>V9</td><td>Utstedersted</td><td>Ja</td><td>A</td><td>Bergen</td><td>Utstedelsessted. Stedsnavn i ”sted og dato” som står ved siden av underskriftene på papirvitnemålet. Dette er et geografisk stedsnavn, for eksempel by, tettsted eller kommune. Ikke navn på skole, organisasjon eller annet.</td></tr>
<tr><td>V10</td><td>Dato_utstedt</td><td>Ja</td><td>D8</td><td>20081224<br/>yyyymmdd</td><td>Utstedelsesdato. Datoen i ”sted og dato”<br/>som står ved siden av underskriftene.</td></tr>
<tr><td>V11</td><td>Skolenavn</td><td>Ja</td><td>A</td><td>Borgen skole</td><td>Utsteders/skolens navn slik det står på<br/>papirdokumentet.</td></tr>
<tr><td>V12</td><td>Rektornavn</td><td>Ja</td><td>A</td><td>Randi Rektor</td><td>Rektor eller den ansvarlige som har<br/>skrevet under. Slik det står på papiret.</td></tr>
<tr><td>V13</td><td>Underskrivernavn</td><td>Ja</td><td>A</td><td>Sara Sekretær</td><td>Den andre personen som skrev under.<br/>Kontaktperson for dokumentet. Det skal<br/>vel alltid være to? ”For Sara Sekretær”</td></tr>
<tr><td>V14</td><td>Foedtdato</td><td>Ja</td><td>N06</td><td style="text-align: right">010871</td><td>formen DDMMÅÅ. Første del av det 11-sifrede norske fødselsnummeret. Kan være et såkalt D-nr som starter på DD+40 (dvs dato 01 blir 41 osv på slike)</td></tr>
<tr><td>V15</td><td>Personnummer</td><td></td><td>N05</td><td style="text-align: right">34567</td><td>Elevens/privatistens/lærlingens person-<br/>nummer. De fem siste sifrene av det 11-sifrede fødselsnummeret. Ikke<br/>obligatorisk, men må settes om SO skal<br/>kunne bruke vitnemålet i opptak. Det<br/>gis FEIL dersom de to bakerste<br/>kontrollsifrene her er ugyldige.</td></tr>
<tr><td>V16</td><td>Personnavn</td><td>Ja</td><td>A</td><td>Erik Elev<br><i>eller</i><br>Etternavn, Erik</td><td>Elevens/privatistens/lærlingens fulle navn. Fornavn, eventuelle mellomnavn og Etternavn med mellomrom mellom. Formen Etternavn komma mellomrom Fornavn Mellomnavn er også ok. Mellomnavn bør skrives fullt ut, men kan skrives som initialer med punktum bak. Slik det står på papirdokumentet.</td></tr>
<tr><td>V17</td><td>Dispensasjonkode</td><td></td><td>A1</td><td>D</td><td>D, F eller blank.<br>
    D = vitnemålet er gitt dispensasjon fra fagkontrollene.<br>
    F = forsøksvitnemål (kun for R94, ugyldig for KL)<br>
    Dersom koden er D skal det finnes minst en ¤M-linje som forklarer årsaken til dispensasjonen.</td></tr>
<tr><td>V18</td><td>Gsk_ok</td><td></td><td>A1</td><td>J</td>
  <td>J, N eller blank.<br><br>
      Kode <b>J</b> angir at det stod noe ala ”...og har oppnådd generell studiekompetanse” på dokumentet.<br><br>
      Feltet brukes av SO til å gjenskape et skjermvitnemål som er mest mulig likt papirvitnemålet.<br><br>
    Kode <b>N</b> her vil gi et VARSEL dersom kontroll.exe finner ut at fagene tilsier at GSK er oppnådd likevel, unntatt for yrkesfaglige vm. (Og kanskje et VARSEL i det omvendte tilfellet også,
    der V18=J uten at gsk er oppnådd ifølge kontrollmotoren)<br><br>
      Trigger teksten ”...og har oppnådd generell studiekompetanse” i SOs fremvisning av vitnemål for søker selv.<br>
Forslag fra Extens:<br>
G=”og har generell studiekompetanse”<br>
F=”og har bestått(?) fagopplæring”<br>
Y=”og har yrkeskompetanse(?)”<br>
...slik at V18 styrer den linjen på vitnemålet</td></tr>
<tr><td>V20</td><td>Omfang</td><td>Ja</td><td>N4</td><td style="text-align: right">2345</td><td>Omfangstallet som står på vitnemålet /<br/>kompetansebeviset. Feltet brukes både<br/>for R94-vitnemål og KL-dokumenter<br/>selv om det er forskjellige tallskalaer.</td></tr>
<tr><td>V22</td><td>Orden</td><td></td><td>A</td><td>N</td><td>Tre gyldige koder i V22 og V23:</td></tr>
<tr><td>V23</td><td>Adferd</td><td></td><td>A</td><td>G</td>
  <td>G = God<br/>
      N = Nokså god<br/>
      L = Lite god<br/>
    Av historiske årsaker godtas også<br/>følgende fem koder/verdier: NG, LG,<br/>God, Nokså god og Lite god. V22 er<br/>obligatorisk når V5=VM eller når det er<br/>ført minst en standpunktkarakter. V23<br/>er obligatorisk når V4=KL og V5=VM</td></tr>
<tr><td>V24</td><td>Antall_vedlegg</td><td>–</td><td>N</td><td style="text-align: right">0</td><td colspan="2">Blank eller et heltall. Angir antall vedlegg til vitnemålet / kompetanse-beviset (antall sider?)</td></tr>
<tr><td>V25</td><td>Filnavn_vedlegg</td><td>–</td><td>A</td><td></td><td colspan="2">Filnavn eller mappenavn for vedleggsdokumentet/-ene i .zip-fil eller .tar.gz-fil. Ikke obligatorisk felt selv om V24 &gt; 0. Se kap. 7.4 side 28.</td></tr>
<tr><td>V26</td><td>Maalformkode</td><td>–</td><td>A1</td><td>B</td><td colspan="2">Fire gyldige koder i V26:<br>
B = bokmål<br>
N = nynorsk<br>
S = samisk<br>
A = annet<br>
Papirdokumentet ble skrevet ut på bokmål, nynorsk, nord-samisk eller annet språk. Kan brukes av SO som foretrukket målform i skjermvisning.</td></tr>
</table>

#### 6.4 Vgdokpromr-linjer ¤P
Vgdokpromr-tabellen har en linje pr programområde på et dokument. Normalt 3 stk pr vitnemål og 1
på kompetansebevis, men kan være 0 (ingen ¤P-linjer) for kompetansebevis med kun fellesfag.
¤P-linjer må ha en ¤V-linje i filen med samme Vgdoknr.

<table>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>P0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤P</td><td>Alltid ¤P</td></tr>
<tr><td>P1</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td>V97995898</td><td>Dokumentidentifikatoren.</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">620080002</td><td>Se V1 side 14.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>(en linje)</td><td></td></tr>
<tr><td>P2</td><td>Promrkode</td><td>Ja</td><td>A</td><td>STUSP1---</td><td>Lovlige koder er programområdekoder</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">-</td><td>som er eller har vært eksportert fra</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Grep. Tilsvarer</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kurskode1-3 i $V-linjene R94.</td></tr>
<tr><td>P3</td><td>Nivaakode</td><td>Ja</td><td>A3</td><td>VG1</td><td>Nivåkode. VG1, VG2, VG3, VG4</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eller VG5. Det kan være flere ¤P-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>linjer med samme nivaakode i samme</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>dokument (under samme ¤V).</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Nivaakode er altså ikke nødvendigvis</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>unikt. For V4=R94 godtas også GK,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>VK1, VK2, VKI og VKII (disse kon-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>verteres internt til VG1, VG2 og VG3)</td></tr>
<tr><td>P4</td><td>Paastandkode</td><td></td><td>A2</td><td>F</td><td>Blank = står ingenting</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>B = Bestått</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>F = Fullført, alle fag er tatt, men ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>bestått</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>FB = Fullført og bestått</td></tr>
<tr><td style="text-align: right" colspan="6">17</td></tr>
</table>
<h2 class="pagenumber">Page 18</h2>
<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td>(”Bestått” står på vitnemålnivå, ikke<br/>pr år, når dokumenttypen er VM).<br/>Kontroll.exe krever at siste års ¤P har<br/>P4 = B eller F eller FB for vitnemål i<br/>KL). Koder H, I og M godtas også.</td></tr>
<tr><td>P5</td><td>Paastand</td><td></td><td>A</td><td>Fullført</td><td>Hva som faktisk stod på dokumentet.</td></tr>
<tr><td>P6</td><td>Fravaer_dager</td><td>Ja 5</td><td>N3</td><td style="text-align: right">0</td><td>Fravær dette året, dager og timer.</td></tr>
<tr><td>P7</td><td>Fravaer_timer</td><td>Ja</td><td>N3</td><td style="text-align: right">12</td><td>Normalt tall uten desimaler. Inkl 0.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Obligatorisk hvis minst en standpunkt-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>karakter finnes.</td></tr>
<tr><td>P8</td><td>Utdprogramkode</td><td></td><td>A</td><td></td><td>Frivillig felt. Bør kunne avledes fra</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>P2-Promrkode og Grep. Utdannings-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>programmet står på dokumentet for</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>hvert programområde. AA, BA, BY,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>DH, EL, FO, HN, HS, ID, KP, MD,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>ME, MK, NA, RM, SA, SS, ST, TB,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>TF, TP eller TR. Noen av de er</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>gyldige kun i KL, andre kun i R94, det</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>fremgår av UDIR-rundskriv.</td></tr>
<tr><td>P9</td><td>Aarstall</td><td></td><td>N4</td><td></td><td>Årstall. Valgfritt. P9 = V6-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Avgangsaar for siste P3 (typisk i</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>VG3).</td></tr>
<tr><td colspan="6">6.5 Vgdokfag-linjer ¤F</td></tr>
<tr><td colspan="6">Det skal eksistere en ¤F-linje for hvert vitnemålsfag som føres på vitnemålet/kompetansebeviset. Det</td></tr>
<tr><td colspan="6">skal være minst en ¤F for hver ¤V. F2-Fagkode inngår i primærnøkkelen, det kan aldri være mer enn</td></tr>
<tr><td colspan="6">en av samme fagkode på samme dokument.</td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>F0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤F</td><td>Alltid ¤F</td></tr>
<tr><td>F1</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td>V979958986200</td><td>Dokumentidentifikatoren.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>80002 (en linje)</td><td>Se V1 side 14.</td></tr>
<tr><td>F2</td><td>Fagkode</td><td>Ja</td><td>A10</td><td>KRO1001</td><td>En fagkode som er eller har vært</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>definert av Grep.</td></tr>
<tr><td>F3</td><td>Fagtypekode</td><td>Ja</td><td>A2</td><td>FF</td><td>Obligatorisk kun for KL-vitnemål.</td></tr>
<tr><td></td><td></td><td>for</td><td></td><td></td><td>FF = Fellesfag</td></tr>
<tr><td></td><td></td><td>KL</td><td></td><td></td><td>FP = Felles programfag</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>VP = Valgfritt programfag</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>PF = Prosjekt til fordypning</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>UP = Uspesifisert programfag</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>GS = Grunnskolefag (ubrukt i</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>NVB)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Gyldige for R94, men ikke KL:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>SF = Studieretningsfag(?)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>VF = Valgfag</td></tr>
<tr><td>F4</td><td>Linjenr</td><td>Ja</td><td>N2</td><td style="text-align: right">23</td><td>Brukes til å bestemme rekkefølgen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>av fagene:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>KL: Sorterer på F3 deretter F4,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>men sett helst F4 slik at rapporter</td></tr>
<tr><td style="text-align: right">5</td><td colspan="4">For dager og timer, P6 og P7: Obligatorisk for vitnemål hvis minst en standpunktkarakter finnes.</td><td></td></tr>
<tr><td></td><td colspan="4"></td><td style="text-align: right">18</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">som fortsatt kun sorterer på F4 får<br/>riktig rekkefølge likevel.<br/>R94: Sorterer kun på F4.</td></tr>
<tr><td>F5</td><td>Karakter_standpunkt</td><td>Ja 6</td><td>A2</td><td>D</td><td>1, 2, 3, 4, 5, 6 eller:</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>D = deltatt</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">F = fritatt, må da sette FAMnn-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>merknadkode</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>R = realkompetanse</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>B = bestått</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">BM = bestått meget godt (eller</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>BMG)</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>IB = ikke bestått</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>GK=Godkjent</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">IV = Ikke vurderingsgrunnlag (kun</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">på komp.bevis og kun i stand-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>punktfeltet)</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">IM = Ikke møtt (kun på</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>komp.bevis og kun i</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eksamensfeltet)</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>0 = Kun i fag fra R94</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">- = ingen karakter (samme som</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>blank)</td><td></td></tr>
<tr><td>F6</td><td>Karakter_eksamen</td><td>Ja"</td><td>A2</td><td style="text-align: right">6</td><td colspan="2">...samme som for F5, men:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>D = Deltatt kan brukes</td><td>i F5, men</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>ikke i F6.</td><td></td></tr>
<tr><td>F7</td><td>Eksamensformkode</td><td></td><td>A2</td><td>S</td><td>Blank</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>S = Skriftlig eksamen</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>M = Muntlig eksamen</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">MP = Muntlig-praktisk eksamen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>P = Praktisk eksamen</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">TS = Tegnspråklig/Skriftlig(?)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">TP = Teoretisk/Praktisk 2</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">(Kontroll.exe vil kontrollere dette</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>mot Grep?)</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">F7 er obligatorisk når F6 er satt</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>(unntatt F og - strek).</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">F6 er obligatorisk og kan ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">være blank/- når F7 er satt.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">Så enten er begge blanke eller</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">begge satt (unntak: F6=F/- og</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>F7=blank)</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">Disse er lov for R94, men ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>KL:</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>P Praktisk</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>S Skriftlig</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>M Muntlig</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>T Tegnspråklig</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>MP Muntlig/Praktisk</td><td></td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>SP Skriftlig/Praktisk</td><td></td></tr>
<tr><td style="text-align: right">6</td><td colspan="4">Bruk - (strek/minustegn) der det er ingen karakter</td><td colspan="2"></td></tr>
<tr><td></td><td colspan="4"></td><td style="text-align: right" colspan="2">19</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td>TP Teoretisk/Praktisk 7<br/>SM Skriftlig/Muntlig<br/>ST Skriftlig/Tegnspråklig<br/>IM Ikke møtt (kun kompetanse-<br/>bevis, kan bli fjernet)</td></tr>
<tr><td>F8</td><td>Omfang</td><td></td><td>N</td><td>Vanlige tall for</td><td>Tallet som står på dokumentet.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>KL nå er 56, 84,</td><td>Blank settes i stedet for -- som</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">112, 140, 159,</td><td>noen bruker i utskrift. F8 settes når</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">168, 169, 197,</td><td>omfangsfallet står ved siden av F2-</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">224, 225, 253,</td><td>fagkoden i utskriften. Hvorvidt F8</td></tr>
<tr><td></td><td></td><td></td><td></td><td>337 og 393. På</td><td>brukes i omfangskontroll, avgjøres</td></tr>
<tr><td></td><td></td><td></td><td></td><td>eldre KL er også</td><td>av om fagkoden har omfang_-</td></tr>
<tr><td></td><td></td><td></td><td></td><td>113, 150 og 365</td><td>overstyrbart = J i fagregisteret.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>vanlige.</td><td></td></tr>
<tr><td>F9</td><td>Terminkode</td><td>Ja</td><td>A1</td><td>V</td><td>Het tidligere eksamenstermin.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Bruker kodene:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>V = vår og</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>H = høst.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>(Før har også måneder 05 og 12</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>vært sendt, men kun V og H nå)</td></tr>
<tr><td>F10</td><td>Aar</td><td>Ja 8</td><td>N4</td><td style="text-align: right">2008</td><td>År. Eksamensår. På dokumentene</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>står for eksempel V14, dette</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>oversettes til F9=V og F10=2014 i</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>filformatet.</td></tr>
<tr><td>F12</td><td>Fagstatuskode</td><td>Ja</td><td>A1</td><td>E</td><td>Personens status i faget:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>E = Elevfag</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>P = Privatistfag</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Kode P gir feilmelding når det er</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>satt standpunktkarakter. Normalt</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kun P eller blank/E til NVB.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>I tillegg godtas også følgende i</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>F12 for KL-vitnemål (men ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>R94), men de vil kunne bli</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>ugyldige i senere versjoner (se det</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>nye feltet F18):</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>R = Realkompetansevurdert i faget</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>F = Fritatt</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>V = Voksenopplæring</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>O = Oppdragsundervisning annen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>institusjon</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>A = Alternativ opplæringsplan,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>tolkes som E i kontrollene.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Følgende koder har vært innsendt,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>men blir pr versjon 15.10 (juni</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>2014) og trolig også tidligere</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>avvist av NVB-importen:</td></tr>
<tr><td style="text-align: right">7</td><td colspan="4">TP og TS skulle være med ihht tlf fra SO 19. mai 2009</td><td></td></tr>
<tr><td style="text-align: right">8</td><td colspan="4">Obligatorisk for KL-vitnemål, skal også settes for R94 om mulig</td><td></td></tr>
<tr><td></td><td colspan="4"></td><td style="text-align: right">20</td></tr>
</table>
<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td>S = sluttet i faget?<br/>K = klage?<br/>N = nettundervisning? (som E?)<br/>O = oppdragsundervisning annen<br/>institusjon?<br/>U = utenlandsk utvekslingselev?<br/>H = ?<br/>Se også det nye feltet F18.</td></tr>
<tr><td>F13</td><td>Merknadkode</td><td>A5</td><td>FAM11</td><td>KL: Hvis merknad, sett en lovlig<br/>Grepmerknadskode her. Kodesettet<br/>kommer fra UDIRs Grep-database.<br/>R94: Ingenting i F13 (foreløpig)</td></tr>
<tr><td>F14</td><td>Merknadparameter</td><td>A</td><td>VG4003</td><td>KL: En parameter til merknaden</td></tr>
<tr><td></td><td></td><td></td><td></td><td>dersom merknadsteksten i Grep</td></tr>
<tr><td></td><td></td><td></td><td></td><td>inneholder &lt;år&gt; eller &lt;fagkode&gt;</td></tr>
<tr><td></td><td></td><td></td><td></td><td>eller &lt;noe annet&gt;. Kan være for</td></tr>
<tr><td></td><td></td><td></td><td></td><td>eksempel et årstall eller en R94-</td></tr>
<tr><td></td><td></td><td></td><td></td><td>fagkode.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>R94: Hele merknaden i F14 og</td></tr>
<tr><td></td><td></td><td></td><td></td><td>F13 trenger ikke være satt.</td></tr>
<tr><td>F15</td><td>Merknadparameter2</td><td>A</td><td></td><td>...som over... Settes når det finnes<br/>to &lt;parametere&gt; i meldingsteksten<br/>fra Grep.</td></tr>
<tr><td>F16</td><td>Fordypningsfag</td><td>A1</td><td>J</td><td>Blank eller J.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>Skal være blank i KL-dokumenter.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>Skal være J i R94-vitnemål hvis</td></tr>
<tr><td></td><td></td><td></td><td></td><td>fagkoden inngår i fordypning,</td></tr>
<tr><td></td><td></td><td></td><td></td><td>føres bare på fagkoden for høyeste</td></tr>
<tr><td></td><td></td><td></td><td></td><td>nivå.</td></tr>
<tr><td>F17</td><td>Fagnavn</td><td>A</td><td></td><td>Normalt blank, men kan være satt</td></tr>
<tr><td></td><td></td><td></td><td></td><td>for valgfag og lignende. Også satt</td></tr>
<tr><td></td><td></td><td></td><td></td><td>hvis det er skrevet et annet</td></tr>
<tr><td></td><td></td><td></td><td></td><td>fagnavn på vitnemålet enn det som</td></tr>
<tr><td></td><td></td><td></td><td></td><td>står i Grep. Kontroll.exe varsler 9</td></tr>
<tr><td></td><td></td><td></td><td></td><td>hvis F17 er satt i KL-dokumenter</td></tr>
<tr><td></td><td></td><td></td><td></td><td>siden KL-dokumenter ikke har</td></tr>
<tr><td></td><td></td><td></td><td></td><td>brukerstyrte fagnavn. F17 brukes</td></tr>
<tr><td></td><td></td><td></td><td></td><td>kun for R94-vitnemål og da typisk</td></tr>
<tr><td></td><td></td><td></td><td></td><td>ved VL....fagkoder (valgfag).</td></tr>
<tr><td>F18</td><td>Faginfokode</td><td>A1</td><td colspan="2">Bakgrunn er bl.a. e-post fra Geir A. 22 Jun 2009 15:33:</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">Vi må bruke F12-fagstatusfeltet til lese om personen er</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">elev eller privatist i faget. Det ser ut som om noen har</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">modellert for bruke F12- feltet til ulike formål. Extens</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">eller VIGO eller noen andre må etter vår oppfatning</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">splitte dette feltet i to, slik at informasjon om klage</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">ikke overskriver status for personen som brukes for å</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">sjekke rett karakterføring. På kort sikt kan dette bare</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">løses ved at verdien settes til E eller P for eksport til</td></tr>
<tr><td></td><td></td><td></td><td colspan="2">oss, og så kan man rette den lokalt</td></tr>
<tr><td></td><td></td><td></td><td>igjen senere.</td><td></td></tr>
<tr><td style="text-align: right">9</td><td colspan="3">Kun varselmelding, ikke feilmelding</td><td></td></tr>
<tr><td></td><td colspan="3"></td><td style="text-align: right">21</td></tr>
</table>
<table class="table table-striped table-bordered"><tr><td colspan="6">Følgende kodesett fra UDIRs definisjonskatalog (?)</td></tr>
<tr><td colspan="6">skal foreløpig ikke inn i F12, men det kan kanskje bli</td></tr>
<tr><td colspan="6">opprettet et nytt felt for de senere.</td></tr>
<tr><td colspan="6">A = alternativ opplæringsplan, spesialundervisning</td></tr>
<tr><td colspan="6">F = fritatt</td></tr>
<tr><td colspan="6">N = nettundervisning</td></tr>
<tr><td colspan="6">U = utenlandsk utvekslingselev i Norge</td></tr>
<tr><td colspan="6">R = realkompetansevurdert (R i F12 eller F18?)</td></tr>
<tr><td colspan="6">V = voksne</td></tr>
<tr><td colspan="6">O = oppdragsundervisning fra annen institusjon</td></tr>
<tr><td colspan="6">S = sluttet i faget</td></tr>
<tr><td colspan="6">Det nye 10 frivillige feltet F18 er svaret på dette.</td></tr>
<tr><td colspan="6">6.6 Vgdokmerknad-linjer ¤M</td></tr>
<tr><td colspan="6">Merknader til enkeltfag eller til dokumentet (vitnemålet) som helhet.</td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>M0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤M</td><td>Alltid ¤M</td></tr>
<tr><td>M1</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td>V979958986200<br/>80002 (en linje)</td><td>Dokumentidentifikatoren.<br/>Se V1 side 14.</td></tr>
<tr><td>M2</td><td>Merknadnr</td><td>Ja</td><td>N</td><td style="text-align: right">2</td><td>Løpenr for merknaden. Unikt<br/>innen vitnemålet. Avgjør rekke-<br/>følgen hvis det er mer enn en<br/>merknad i et vitnemål.</td></tr>
<tr><td>M3</td><td>Merknadkode</td><td>Ja</td><td>A5</td><td>VMM02</td><td>VMMnn der nn er et tosifret tall.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Som definert av Grep. Kun VMM-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>merknader, ikke FAMnn. FAMnn</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>settes i F12 i ¤F.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>For R94-vitnemål med ikke-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kodifiserte merknader (kun tekst)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>er M3 = VMR94 og hele</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>merknadsteksten ligger i M4.</td></tr>
<tr><td>M4</td><td>Merknadparameter</td><td></td><td>A</td><td style="text-align: right">2004</td><td>For eksempel et årstall eller en</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>R94-fagkode. Om M4 og M5 skal</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>settes kommer an på om</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>merknadsteksten tilknyttet M3-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>merknadskode krever en eller to</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>parametere.</td></tr>
<tr><td>M5</td><td>Merknadparameter2</td><td></td><td>A</td><td></td><td></td></tr>
<tr><td>M6</td><td>Merknadkategorikode</td><td></td><td>A4</td><td>DISP</td><td>Mulige verdier for R94-vitnemål:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>DISP = dispensasjonsmerknad</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>PRIM =</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>primærvitnemålsmerknad 11</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>FORS = forsøksmerknad</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>ANN = andre vitnemålmerknader</td></tr>
<tr><td style="text-align: right">10</td><td colspan="3">Sep. 2014</td><td colspan="2"></td></tr>
<tr><td style="text-align: right">11</td><td colspan="3">Brukes også til første</td><td colspan="2"></td></tr>
<tr><td></td><td colspan="3"></td><td style="text-align: right" colspan="2">22</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td colspan="5">For KL-vitnemål er M6 alltid<br/>enten DISP eller ingenting (blank).<br/>DISP = dispensasjonsmerknad</td></tr>
<tr><td>M7</td><td>Sidekode</td><td>A1</td><td>F</td><td colspan="5">F eller B. (Forsiden eller baksiden)</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">Angir hvor merknaden på</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">dokumentet stod.</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">Aldri B for KL-dokumenter.</td></tr>
<tr><td>M8</td><td>Promrkode</td><td>A</td><td></td><td colspan="5">Settes sjelden. Kun hvis</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">merknaden tilhører et program-</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">område (en av ¤P-linjene) og ikke</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">vitnemålet som helhet.</td></tr>
<tr><td>M9</td><td>Linjenr</td><td>N</td><td></td><td colspan="5">Brukes bare for R94-vitnemål (se</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">under)</td></tr>
<tr><td>M10</td><td>Merknadtekst</td><td>A</td><td></td><td colspan="5">Brukes bare for R94-vitnemål (se</td></tr>
<tr><td></td><td></td><td></td><td></td><td colspan="5">under)</td></tr>
<tr><td colspan="9">Anbefaler å ikke bruke M9 og M10, de skal helst være blanke. Men hvis de brukes skal det være kun</td></tr>
<tr><td colspan="9">for R94-vitnemål (altså vitnemål der V4=R94). Hvis en av dem er satt skal begge være satt.</td></tr>
<tr><td colspan="9">SO/NVB ønsker at også R94-merknader sendes inn på den nye måten. Dvs at en merknad på R94-</td></tr>
<tr><td colspan="9">vitnemålet som går over flere linjer må slås sammen til en ¤M-linje her. M3 settes da til koden</td></tr>
<tr><td colspan="9">VMR94 og M4 inneholder merknadsteksten, mens M9 og M10 er blanke. M4 kan nå inneholde et eller</td></tr>
<tr><td colspan="9">flere linjeskift. Linjeskifttegn skal ihht ”5.8 Linjeskift i dataene” (side 10) enten erstattes av de to</td></tr>
<tr><td colspan="9">tegnene \n eller så skal hele feltet pakkes inn i { }. Altså { som første tegn og } som siste i</td></tr>
<tr><td colspan="9">meldingsteksten. \n er det anbefalte valget, da unngås linjer i filen som ikke starter på ¤.</td></tr>
<tr><td colspan="5">En M-linje for et R94-vitnemål vil kunne se slik ut:</td><td></td><td></td><td></td><td></td></tr>
<tr><td colspan="5">¤M¤V97995898620080002¤1¤VMR94¤Her er første merknadslinje\nog</td><td>her</td><td>er</td><td>linje</td><td>to¤¤ANN¤F¤¤¤</td></tr>
<tr><td colspan="5">6.7 Vgdokannullering-linjer ¤D</td><td></td><td></td><td></td><td></td></tr>
<tr><td colspan="9">Når utstederskole retter, endrer eller trekker tilbake dokumenter (vitnemål/kompetansebevis), skal det</td></tr>
<tr><td colspan="9">utstedte dokumentet annulleres. Evt nytt dokument som erstatter det gamle, skal ha nytt vgdoknr.</td></tr>
<tr><td colspan="9">Når dokumentet annulleres, skal det lages en ¤D-transaksjon i det skoleadministrative systemet, som</td></tr>
<tr><td colspan="9">oversendes som ¤D-linjer til NVB. I NVB slettes ikke annullerte vitnemål, men blir liggende med</td></tr>
<tr><td colspan="9">vitnemålstatuskode A, noe som tas hensyn til i SO-systemet slik at annullerte vitnemål ikke brukes</td></tr>
<tr><td colspan="9">selv om de kan vises frem både til saksbehandler og eleven selv. (Da med annulleringsinformasjon i</td></tr>
<tr><td colspan="5">rød skrift i tillegg)</td><td></td><td></td><td></td><td></td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td colspan="4">Forklaring</td></tr>
<tr><td>D0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤D</td><td colspan="4">Alltid ¤D</td></tr>
<tr><td>D1</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td>V979958986200</td><td colspan="4">Dokumentidentifikatoren.</td></tr>
<tr><td></td><td></td><td></td><td></td><td>80002 (en linje)</td><td colspan="4">Se V1 side 14.</td></tr>
<tr><td>D2</td><td>Dato_annullert</td><td>Ja</td><td>D8</td><td style="text-align: right">20080909</td><td colspan="4">Datoen når annulleringsvedtaket</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="4">ble gjort. Dato på formen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="4">ÅÅÅÅMMDD.</td></tr>
<tr><td>D3</td><td>Saksbehandler</td><td>Ja</td><td>A</td><td>Anna Annullerer</td><td colspan="4">Saksbehandler som annullerte</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="4">dokumentet. Den SO, univ. og</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="4">høgskoler kan kontakte ved</td></tr>
<tr><td style="text-align: right" colspan="9">23</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">spørsmål. (Vises til søker? Husker<br/>ikke...)<br/>Bruk helst personens navn, ikke<br/>brukernavnet.</td></tr>
<tr><td>D4</td><td>Vgdoknr_erstattes_av</td><td></td><td>A18</td><td>V979958986200</td><td colspan="2">Hvilket dokument som erstatter</td></tr>
<tr><td></td><td></td><td></td><td></td><td style="text-align: right">80020</td><td colspan="2">dette. Trenger ikke å være satt,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">men bør settes hvis det er kjent.</td></tr>
<tr><td>D5</td><td>Annulleringaarsak-</td><td>Ja</td><td>A3</td><td>KLA</td><td colspan="2">Årsak til annullering. Kodene er</td></tr>
<tr><td></td><td>kode</td><td></td><td></td><td></td><td colspan="2">som i R94:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">KLA=Ny karakter etter klage.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">FEI=Pga feilføring</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">NVB=Annullert av NVB sentralt,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">for eksempel etter fax</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">m/underskrift. (Intern SOkode)</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">ANN=Annen årsak.</td></tr>
<tr><td>D6</td><td>Merknad</td><td></td><td>A</td><td>...tekst...</td><td colspan="2">Hvis man har en merknad/-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td colspan="2">kommentar om annulleringen.</td></tr>
<tr><td colspan="7">6.8 Rekkefølgen av linjetyper</td></tr>
<tr><td>•</td><td colspan="6">Filene kan bestå av flere deler der hver del starter med en ¤A-linje fulgt av en frivillig ¤S-linje.</td></tr>
<tr><td>•</td><td colspan="6">Under ¤A (og eventuelt ¤S) ligger en ¤V- eller en eller flere ¤D-linjer.</td></tr>
<tr><td>•</td><td colspan="6">Under hver ¤V-linje ligger en-fem (normalt tre for vitnemål) ¤P-linjer, deretter noen ¤F-linjer</td></tr>
<tr><td></td><td colspan="6">(snitt rundt 20 stk) og så null, en eller flere ¤M-linjer.</td></tr>
<tr><td colspan="7">Det er altså ikke ønskelig at alle ¤A-linjene kommer øverst i filen, de skal innlede hver sin del.</td></tr>
<tr><td colspan="7">(Dersom likevel alle ¤A-linjer ligger øverst, men hver fildel innledes med ¤S, så kan NVBimporten</td></tr>
<tr><td colspan="7">likevel finne ut hvilken ¤A en ¤V tilhører ved å telle antal ¤V under hver ¤S og så sammenligne med</td></tr>
<tr><td colspan="7">antall ¤V hver ¤A sier den skal ha (og tilsvarende for antall ¤P, ¤F, ¤M og ¤D, men dette er usikkert</td></tr>
<tr><td colspan="7">siden to ¤A’er kan ha samme orgnr og samme antall ¤V, ¤P, ¤F, ¤M og ¤D). Denne usikkerheten gjør</td></tr>
<tr><td colspan="7">av NVB ikke skal stole på hvilket versjonsnr av f.eks. SATS som har generert et vitnemål eller hvilken</td></tr>
<tr><td colspan="7">dato/klokkeslett fildelen ble generert.</td></tr>
<tr><td colspan="7">Derfor er det ønskelig å få ¤A-linjene før hver fildel i stedet for å få alle i toppen av filen.</td></tr>
<tr><td colspan="7">6.9 Eksempel på inputfil</td></tr>
<tr><td colspan="7">Denne eksempelfilen har to fildeler (2 stk ¤A), begge med ett vitnemål hver. Den første har i tillegg en</td></tr>
<tr><td colspan="7">vitnemålsannullering.</td></tr>
<tr><td colspan="5">¤A¤874544582¤01020¤1¤1¤22¤3¤1¤IST-EXTENS¤9.1¤20140911130900</td><td></td><td></td></tr>
<tr><td colspan="5">¤S¤874544582¤¤01020¤¤¤Øst videregående skole¤01¤05¤¤F¤¤¤Kristin</td><td></td><td></td></tr>
<tr><td colspan="5">Kontakt¤tittel¤krk@vgs.no¤99999999¤Kine Kontakt¤¤kik@vgs.no¤99999998¤Pb</td><td colspan="2">357¤¤1732¤HØTTEN¤¤¤¤¤¤</td></tr>
<tr><td colspan="5">69162400¤69162599¤st.olav.vgs@ostfold-f.kommune.no¤www.skolen.no</td><td></td><td></td></tr>
<tr><td colspan="5">¤V¤V87454458220140268¤N¤KL¤VM¤2014¤874544582¤Sarpsborg¤20140909¤Øst</td><td>videregående</td><td>skole¤Randi</td></tr>
<tr><td colspan="5">Rektor¤Sara Sekretær¤241294¤44444¤Erik Elev¤¤J¤2608¤G¤G¤0¤¤B</td><td></td><td></td></tr>
<tr><td colspan="5">¤P¤V87454458220140268¤PBPBY4YK--¤VG3¤B¤Bestått¤0¤0¤¤2014</td><td></td><td></td></tr>
<tr><td colspan="5">¤P¤V87454458220140268¤RMKOK3----¤VG3¤B¤Bestått¤0¤0¤¤2013</td><td></td><td></td></tr>
<tr><td colspan="5">¤P¤V87454458220140268¤RMKOS2----¤VG2¤B¤Bestått¤9¤9¤¤2012</td><td></td><td></td></tr>
<tr><td colspan="5">¤P¤V87454458220140268¤RMRMF1----¤VG1¤B¤Bestått¤2¤9¤¤2011</td><td></td><td></td></tr>
<tr><td colspan="5">¤F¤V87454458220140268¤ENG1003¤FF¤6¤6¤¤¤140¤V¤2012¤E¤¤¤¤¤</td><td></td><td></td></tr>
<tr><td colspan="5">¤F¤V87454458220140268¤HIS1003¤FF¤6¤¤4¤M¤140¤V¤2014¤P¤¤¤¤¤</td><td></td><td></td></tr>
<tr><td colspan="5">...flere ¤F-linjer som ikke tas med i eksempelet her</td><td></td><td></td></tr>
<tr><td colspan="5">¤M¤V87454458220140268¤1¤VMM31¤Bestått fagopplæring 12.08.2014¤¤¤F¤¤¤</td><td></td><td></td></tr>
<tr><td colspan="5">¤D¤V97459086720140063¤20140623¤Anne Annullerer¤V97459086720140255¤FEI¤</td><td></td><td></td></tr>
<tr><td colspan="5">¤A¤974544482¤01042¤1¤0¤23¤3¤2¤IST-EXTENS¤9.1¤20140911130901</td><td></td><td></td></tr>
<tr><td colspan="7">¤S¤974544482¤¤01042¤¤¤Livets videregående skole¤01¤06¤¤F¤¤¤Kåre Kontakt¤tittel¤kk@vgs.no¤¤¤¤¤¤</td></tr>
<tr><td colspan="7">Pb 104¤Portveien 2¤6440¤Ellywood¤¤¤¤¤¤69005600¤69005601¤glemmen.vgs@ostfoldfk.no¤</td></tr>
<tr><td colspan="5">www.glemmen.vgs.no</td><td></td><td></td></tr>
<tr><td style="text-align: right" colspan="7">24</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td colspan="6">¤V¤V97454448220140214¤N¤KL¤VM¤2013¤974544482¤Fredrikstad¤20140908¤Livets videregående</td></tr>
<tr><td colspan="6">skole¤Reidun Rektor¤Sigrun Sekretær¤241295¤33333¤Ellen Elev¤¤J¤263¤G¤G¤0¤¤B</td></tr>
<tr><td colspan="6">¤P¤V97454448220140214¤HSHEA2----¤VG2¤B¤Bestått¤9¤12¤¤2010</td></tr>
<tr><td colspan="6">¤P¤V97454448220140214¤HSHEA3----¤VG3¤B¤Bestått¤0¤0¤¤2013</td></tr>
<tr><td colspan="6">¤P¤V97454448220140214¤HSHSF1----¤VG1¤B¤Bestått¤5¤27¤¤2009</td></tr>
<tr><td colspan="6">¤P¤V97454448220140214¤PBPBY4YK--¤VG3¤B¤Bestått¤0¤0¤¤2014</td></tr>
<tr><td colspan="6">¤F¤V97454448220140214¤ENG1003¤FF¤6¤6¤¤¤140¤V¤2010¤E¤¤¤¤¤</td></tr>
<tr><td colspan="6">¤F¤V97454448220140214¤HIS1002¤FF¤6¤¤3¤M¤169¤V¤2012¤P¤¤¤¤¤</td></tr>
<tr><td colspan="4">...flere ¤F-linjer som ikke tas med i eksempelet her</td><td colspan="2"></td></tr>
<tr><td colspan="4">¤M¤V97454448220140214¤1¤VMM11¤¤¤¤F¤¤¤</td><td colspan="2"></td></tr>
<tr><td colspan="4">¤M¤V97454448220140214¤2¤VMM31¤Bestått fagopplæring</td><td colspan="2">17.08.2012¤¤¤F¤¤¤</td></tr>
<tr><td colspan="6">7 Feltene i resultatfilen</td></tr>
<tr><td colspan="6">Første linje i resultatfilen starter alltid på ¤R</td></tr>
<tr><td colspan="6">Så kommer null, en eller flere meldingslinjer som starter på ¤E og som har blank E2-Kontrollnr.</td></tr>
<tr><td colspan="6">Deretter kommer null, en eller flere kontrollresultater ¤K som hver har null, en eller flere ¤E (med E2-</td></tr>
<tr><td colspan="6">Kontrollnr satt) og så null, en eller flere ¤L under seg, så null en eller flere ¤O, før det eventuelt</td></tr>
<tr><td colspan="6">kommer en ny ¤K.</td></tr>
<tr><td colspan="6">Eksempel på linjerekkefølge som viser starten i hver linje på en resultatfil med 16 linjer:</td></tr>
<tr><td colspan="6">¤R¤...</td></tr>
<tr><td colspan="6">¤E¤...</td></tr>
<tr><td colspan="6">¤E¤...</td></tr>
<tr><td colspan="6">¤K¤1¤...</td></tr>
<tr><td colspan="6">¤E¤...</td></tr>
<tr><td colspan="6">¤E¤...</td></tr>
<tr><td colspan="6">¤L¤...</td></tr>
<tr><td colspan="6">¤L¤...</td></tr>
<tr><td colspan="6">¤L¤...</td></tr>
<tr><td colspan="6">¤O¤...</td></tr>
<tr><td colspan="6">¤O¤...</td></tr>
<tr><td colspan="6">¤K¤2¤...</td></tr>
<tr><td colspan="6">¤L¤...</td></tr>
<tr><td colspan="6">¤K¤3¤...</td></tr>
<tr><td colspan="6">¤E¤...</td></tr>
<tr><td colspan="6">¤O¤...</td></tr>
<tr><td colspan="6">Første ¤K har to ¤E, tre ¤L og to ¤O</td></tr>
<tr><td colspan="6">Andre ¤K har ingen ¤E, en ¤L og ingen ¤O</td></tr>
<tr><td colspan="6">Tredje ¤K har en ¤E, ingen ¤L og ingen ¤O</td></tr>
<tr><td style="text-align: right">7.1</td><td colspan="3">Resultatfiltopplinjen ¤R</td><td></td><td></td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>R0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤R</td><td>Alltid ¤R</td></tr>
<tr><td>R1</td><td>Versjon</td><td>Ja</td><td>A10</td><td>10.0a</td><td>Versjon av lokal kontroll.exe</td></tr>
<tr><td>R2</td><td>Versjonsdato</td><td>Ja</td><td>D8</td><td style="text-align: right">20080917</td><td>Når versjonen i R1 ble distribuert første<br/>gang</td></tr>
<tr><td>R3</td><td>Versjon_server</td><td></td><td>A10</td><td style="text-align: right">10.1</td><td>Hvilken versjon kontrollene er kjørt mot.<br/>R3 settes kun ved bruk av -s eller -S.</td></tr>
<tr><td>R4</td><td>Versjonsdato_se<br/>rver</td><td></td><td>D8</td><td style="text-align: right">20080919</td><td>Når versjonen i R3 ble distribuert eller tatt i<br/>bruk offentlig første gang. R4 settes kun<br/>ved bruk av -s eller -S.</td></tr>
<tr><td>R5</td><td>Versjonsdato_ne<br/>ste</td><td>Ja</td><td>D8</td><td style="text-align: right">20081015</td><td>Når neste versjon av nedlastbar<br/>kontroll.exe antas å være klar. Det gis et<br/>varsel (rød tekst) i resultatrapporten når</td></tr>
<tr><td style="text-align: right" colspan="6">25</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td>datoen er utløpt. Innen R5 bør alle sjekke<br/>om det finnes en nyere versjon på<br/>www.samordnaopptak.no/nvb og installere<br/>den.</td></tr>
<tr><td>R6</td><td>Tidspunkt</td><td>Ja</td><td>T14</td><td>2008091714<br/>5100</td><td>Når resultatfilen ble laget</td></tr>
<tr><td>R7</td><td>Kjoeretid</td><td>Ja</td><td>N6.2</td><td style="text-align: right">123.45</td><td>Kjøretid i sekunder.<br/>Ved bruk av -S er dette kjøretiden fra<br/>server mottok input til resultatet ble sendt<br/>tilbake.</td></tr>
<tr><td>R8</td><td>Antall_linjer</td><td>Ja</td><td>N8</td><td style="text-align: right">234</td><td>Antall linjer i filen, inkl denne ¤R-linjen.</td></tr>
<tr><td>R9</td><td>Antall_E_linjer</td><td>Ja</td><td>N8</td><td style="text-align: right">7</td><td>Antall ¤E-linjer i filen.</td></tr>
<tr><td>R10</td><td>Antall_K_linjer</td><td>Ja</td><td>N8</td><td style="text-align: right">13</td><td>Antall ¤K-linjer i filen. Dvs antall<br/>kontrollerresultater. Normalt = antall<br/>dokumenter kontrollert.</td></tr>
<tr><td>R11</td><td>Antall_L_linjer</td><td>Ja</td><td>N8</td><td style="text-align: right">49</td><td>Antall ¤L-linjer i filen. Antall logg-linjer.</td></tr>
<tr><td style="text-align: right">7.2</td><td colspan="3">Kontrollresultat-linjer ¤K</td><td></td><td></td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>K0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤K</td><td>Alltid ¤K</td></tr>
<tr><td>K1</td><td>Kontrollnr</td><td>Ja</td><td>N7</td><td style="text-align: right">1</td><td>Entydig løpenr. 1, 2, 3 osv for å skille<br/>kontrollene fra hverandre i ¤E og ¤L. Ingen<br/>¤K i samme fil har samme K1.</td></tr>
<tr><td>K2</td><td>Vgdoknr</td><td>Ja</td><td>A18</td><td></td><td>Vgdoknr, dokumentidentifikator.<br/>Vitnemålsnr eller kompetansebevisnr til<br/>dokumentet som dette resultatet gjelder.</td></tr>
<tr><td>K3</td><td>Kravkode</td><td>Ja</td><td>A40</td><td></td><td>Hvilket krav kontrollen ble kjørt mot. Styrt<br/>av enten –k eller ¤P-linjen(e) når –k<br/>mangler (noe den normalt gjør)</td></tr>
<tr><td>K4</td><td>Tidspunkt</td><td>Ja</td><td>T14</td><td>2008091716<br/>2800</td><td>Når kontrollen startet. Dato og klokkeslett.</td></tr>
<tr><td>K5</td><td>Resultattype</td><td>Ja</td><td>A1</td><td>B</td><td>Boolsk, Tall eller Error.<br/>Tre lovlige koder: B, T eller E.</td></tr>
<tr><td>K6</td><td>Resultatkode</td><td></td><td>A5</td><td>SANN</td><td>Blank, SANN eller USANN.<br/>Blank dersom K5 = T eller E. Satt dersom<br/>K5=B (til SANN eller USANN).</td></tr>
<tr><td>K7</td><td>Resultattall</td><td></td><td>N9.4</td><td style="text-align: right">45.2</td><td>Blank eller et desimaltall. Inneholder tall<br/>dersom K5=T.</td></tr>
<tr><td>K8</td><td>Antall_fag</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K9</td><td>Omfang</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K10</td><td>Vekt</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K11</td><td>Antall_tallkarakt<br/>erer</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K12</td><td>Sum_tallkarakte<br/>rer</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K13</td><td>Snitt_tallkarakte<br/>rer</td><td></td><td>N9.4</td><td></td><td></td></tr>
<tr><td>K14</td><td>Antall_fordypni<br/>nger_foert</td><td></td><td>N6</td><td></td><td></td></tr>
<tr><td>K15</td><td>Var1</td><td></td><td>A200</td><td></td><td>K15-K18 er interne felt som kun brukes</td></tr>
<tr><td>K16</td><td>Var2</td><td></td><td>A200</td><td></td><td>under debugging / feilfinning</td></tr>
<tr><td style="text-align: right" colspan="6">26</td></tr>
</table>
<table class="table table-striped table-bordered"><tr><td>K17</td><td>Var3</td><td colspan="4">A200</td></tr>
<tr><td>K18</td><td>Var4</td><td colspan="4">A200</td></tr>
<tr><td colspan="6">7.3 Feilmeldings-/meldingslinjer ¤E (error)</td></tr>
<tr><td colspan="6">Navnet E (for error) kan være misvisende siden meldingene kan også være av typen TIPS og annet</td></tr>
<tr><td colspan="6">som ikke er feil. Bokstaven E er likevel beholdt fra det gamle formatet.</td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>E0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤E</td><td>Alltid ¤E</td></tr>
<tr><td>E1</td><td>Meldingsnr</td><td>Ja</td><td>N8</td><td style="text-align: right">3</td><td>Entydig løpenr 1, 2, 3 osv. Ingen ¤E i<br/>samme fil har samme E1.</td></tr>
<tr><td>E2</td><td>Kontrollnr</td><td></td><td>N7</td><td style="text-align: right">1</td><td>Blank eller et K1-Kontrollnr fra en ¤K i</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>samme fil. Blank dersom dette er en</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>generell melding uavhengig av et bestemt</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>dokument. Satt dersom meldingen gjelder</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>et dokument.</td></tr>
<tr><td>E3</td><td>Meldingsklasse</td><td>Ja</td><td>A6</td><td>FIL</td><td>Sier om dette er en melding fra</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>filkontrollene, fagkontrollene eller en</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>system-feilmelding. Lovlige koder:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>SYSTEM</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>FIL</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>FAG</td></tr>
<tr><td>E4</td><td></td><td>Ja</td><td>A6</td><td>FEIL</td><td>Sier noe om alvorlighetsgraden. Kodene er:</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>UTGÅTT – Vil trolig ikke brukes, men</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>kan brukes dersom man ønsker å ha med</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>seg ”pensjonerte” meldinger som enten er</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>utgått</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eller overtatt av andre.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>INFO – Ingen feil, men kanskje noe som er</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>nyttig å vite.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>TIPS – Hint om hva som kanskje er feil,</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>for</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eksempel manglende fagkoder.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>VARSEL – Kanskje feil, må sannsynligvis</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>rettes.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>FEIL – Feilmelding. Må rettes. Vitnemål</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>med en eller flere E4=FEIL importeres</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>ikke i NVB og skal ikke skrives ut.</td></tr>
<tr><td>E5</td><td>Meldingskode</td><td>Ja</td><td>A5</td><td>KM101</td><td>Feilkoden. Denne feilkoden er konstant</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>selv om selve feilteksten i E6 endres pga</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>skrivefeil og annet. Muliggjør egen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>dokumentasjon på</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>nettet.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>KM000-KM099 Er systemfeil</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>(E3=SYSTEM),</td></tr>
<tr><td style="text-align: right" colspan="6">27</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td></td><td></td><td></td><td></td><td></td><td>KM100-KM499 er filkontrollmeldinger<br/>(E3=FIL),<br/>KM500-KM999 er fagkontrollmeldinger<br/>(dvs E3=FAG for disse).</td></tr>
<tr><td>E6</td><td>Meldingstekst</td><td>Ja</td><td>A</td><td>Fag</td><td>Selve meldingsteksten. Linjeskift i</td></tr>
<tr><td></td><td></td><td></td><td></td><td>&lt;fagkode&gt;</td><td>meldingen vil være erstattet av de to</td></tr>
<tr><td></td><td></td><td></td><td></td><td>mangler</td><td>tegnene \n på filen (dette gjelder også E7-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>E10).</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Ved bruk av kjøreopsjon –p vil</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>meldingsteksten bli parametrisert og felt</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>E7-E10 kan bli tatt i bruk. At</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>meldingsteksten parametriseres betyr at</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>meldingsteksten inneholder plassavholdere</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>som for eksempel &lt;årstall&gt; eller</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>&lt;fagkode&gt; der brukeren skal se et årstall</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eller</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>en fagkode.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>Altså &lt; fulgt av en eller flere bokstaver, tall</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>eller tegnet _ fulgt av &gt;. Alt uten</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>mellomrom.</td></tr>
<tr><td>E7</td><td>Parameter1</td><td></td><td>A</td><td>NOR4004</td><td>Plugges inn i første [felt] når E6-meldingen</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>skal vises frem. Evt med egen farge for</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>parameterverdien.</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>E7-E10 vil aldri være satt uten at kjøre-</td></tr>
<tr><td></td><td></td><td></td><td></td><td></td><td>opsjon –p er brukt. Mer om den side 6.</td></tr>
<tr><td>E8</td><td>Parameter2</td><td></td><td>A</td><td style="text-align: right">2008</td><td>Plugges inn i andre [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E9</td><td>Parameter3</td><td></td><td>A</td><td>AA1005</td><td>Plugges inn i tredje [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E10</td><td>Parameter4</td><td></td><td>A</td><td>VG4064</td><td>Plugges inn i fjerde [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E11</td><td>Parameter5</td><td></td><td>A</td><td></td><td>Plugges inn i femte [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E12</td><td>Parameter6</td><td></td><td>A</td><td></td><td>Plugges inn i sjette [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E13</td><td>Parameter7</td><td></td><td>A</td><td></td><td>Plugges inn i sjuende [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E14</td><td>Parameter8</td><td></td><td>A</td><td></td><td>Plugges inn i åttende [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E15</td><td>Parameter9</td><td></td><td>A</td><td></td><td>Plugges inn i niende [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td>E16</td><td>Parameter10</td><td></td><td>A</td><td></td><td>Plugges inn i tiende [felt] i E6 når<br/>meldingen vises frem. Evt med egen farge.</td></tr>
<tr><td colspan="6">7.4 Logglinjer ¤L</td></tr>
<tr><td colspan="6">Loggen fra Fagkontrollene.</td></tr>
<tr><td>Felt</td><td>Feltnavn</td><td>Obl-</td><td>For-</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>nr</td><td></td><td>ig.</td><td>mat</td><td></td><td></td></tr>
<tr><td style="text-align: right" colspan="6">28</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td>L0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤L</td><td>Alltid ¤L</td></tr>
<tr><td>L1</td><td>Kontrollnr</td><td>Ja</td><td>N</td><td style="text-align: right">1</td><td>Et K1-Kontrollnr fra en ¤K i samme fil.<br/>Hvilken kontroll (dokument, vitnemål)<br/>denne logglinjen tilhører.</td></tr>
<tr><td>L2</td><td>Linjenr</td><td>Ja</td><td>N</td><td style="text-align: right">23</td><td>Linjenr innen hver kontroll. Øverste ¤L<br/>under hver ¤K starter på 1.</td></tr>
<tr><td>L3</td><td>Loggtekst</td><td></td><td>A</td><td></td><td>Denne linjen i loggen.</td></tr>
<tr><td colspan="6">7.5 Oppbrukte fag ¤O</td></tr>
<tr><td colspan="6">Kontroller som resulteter i SANN (eller et tall) har en liste av fagkoder den behøvdes for å gjøre</td></tr>
<tr><td colspan="6">kontrollen SANN... Dette kalles oppbrukte fag fordi det internt i kontrollene ofte er slik at en fagkode</td></tr>
<tr><td colspan="6">ikke kan brukes til å dekke flere krav...</td></tr>
<tr><td>Felt<br/>nr</td><td>Feltnavn</td><td>Obl-<br/>ig.</td><td>For-<br/>mat</td><td>Eksempel</td><td>Forklaring</td></tr>
<tr><td>O0</td><td>Linjetype</td><td>Ja</td><td>A2</td><td>¤O</td><td>Alltid ¤O</td></tr>
<tr><td>O1</td><td>Kontrollnr</td><td>Ja</td><td>N</td><td style="text-align: right">1</td><td>Et K1-Kontrollnr fra en ¤K i samme fil.</td></tr>
<tr><td>O2</td><td>Fagkode</td><td>Ja</td><td>A10</td><td>UPF3003</td><td>En fagkode fra det vgdok-et som ble<br/>kontrollert. Dette finnes i K2-Vgdoknr i<br/>¤K-linjen med samme kontrollnr.</td></tr>
<tr><td colspan="6">8 Vedlegg, zip-fil</td></tr>
<tr><td colspan="6">Det er åpnet for innsending av vedleggfiler for vedlegg til vitnemålene sammen med en vanlig datafil,</td></tr>
<tr><td colspan="6">men det er ikke påkrevd i første omgang. Vedleggfilene må ha et utskrivbart format. PDF- eller Word-</td></tr>
<tr><td colspan="6">filer f.eks.</td></tr>
<tr><td colspan="6">Innsending skjer ved å sende inn både datafilen (med ¤-linjene) og vedleggsfilene pakket i en .ZIP-fil.</td></tr>
<tr><td colspan="6">V25-feltet (side 17) pluss evt M9 (side 23) i hvert dokument (dokumentmerknad) angir vedleggets</td></tr>
<tr><td colspan="6">filnavn i .zip-filen.</td></tr>
<tr><td colspan="6">Datafilen i den innsendte .zip-filen må ha nøyaktig samme navn som .zip-filen, bortsett fra .zip-</td></tr>
<tr><td colspan="6">filendelsen. Det for å kunne vite hvilken av filene i .zip som er datafilen. Unngå også undermapper</td></tr>
<tr><td colspan="6">i .zip-filen.</td></tr>
<tr><td colspan="6">.tar-filer (eller .tar.gz eller .tgz) er et alternativ til .zip som er mer vanlig i Linux, de samme regler</td></tr>
<tr><td colspan="6">skissert over gjelder her, bortsett fra pakkeformat og filendelse.</td></tr>
<tr><td colspan="6">9 Organisasjonsnummer, kontrollsiffer</td></tr>
<tr><td colspan="6">SOs NVB-database bruker orgnr som primærnøkkel for dokumentutsteder.</td></tr>
<tr><td colspan="6">9.1 Gyldige orgnr</td></tr>
<tr><td colspan="6">Alle orgnr-felter i filen skal være et ni-sifret organisasjonsnummer som normalt starter på 8 eller 9 og</td></tr>
<tr><td colspan="6">finnes i NVBs skoleregister og hos www.brreg.no. Vha det bakerste sifferet i orgnr’et kan man avgjøre</td></tr>
<tr><td colspan="6">om orgnr er et gyldig orgnr, eller om det har skjedd en tastefeil. De ni sifrene skal ligge etter hverandre</td></tr>
<tr><td colspan="6">på filen uten mellomrom eller andre skilletegn. (Hvordan man velger å vise det på skjermen eller ta i</td></tr>
<tr><td colspan="6">mot inntasting er en uavhengig sak).</td></tr>
<tr><td style="text-align: right" colspan="6">29</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td colspan="11">Det bør heller ikke være slik at skolene må taste inn orgnr for hver filinnsending eller lignende. Det</td></tr>
<tr><td colspan="11">bør evt ligge fast i ”Preferences” eller i en .INI-fil på det skoleadministrative systemet. Og der skolene</td></tr>
<tr><td colspan="11">evt gis mulighet til å endre eller sette orgnr, kan det være en tekst a la ”husk å kontrollere dette mot</td></tr>
<tr><td colspan="11">skoleregisteret på www.samordnaopptak.no/nvb for unngå at du bruker feil eller en annen skoles</td></tr>
<tr><td colspan="11">orgnr”.</td></tr>
<tr><td colspan="11">9.2 Kontrollsiffer</td></tr>
<tr><td colspan="11">Det bakerste sifferet er et kontrollsiffer som avledes av de åtte sifrene foran. Poenget med</td></tr>
<tr><td colspan="11">kontrollsifferet er å hindre gale inntastinger. (Fødselsnummer, VISA-nummer, og ofte KIDnumre på</td></tr>
<tr><td colspan="11">regninger har også lignende kontrollsifre). Algoritmen for kontroll av riktig orgnr er slik: (det gyldige</td></tr>
<tr><td colspan="11">orgnr 842872022 brukes her som eksempel):</td></tr>
<tr><td></td><td>Siffer 1</td><td>Siffer 2</td><td>Siffer 3</td><td>Siffer 4</td><td>Siffer 5</td><td>Siffer 6</td><td>Siffer 7</td><td>Siffer 8</td><td>Siffer 9</td><td>Sum</td></tr>
<tr><td>O=orgnr</td><td style="text-align: right">8</td><td style="text-align: right">4</td><td style="text-align: right">2</td><td style="text-align: right">8</td><td style="text-align: right">7</td><td style="text-align: right">2</td><td style="text-align: right">0</td><td style="text-align: right">2</td><td style="text-align: right">2</td><td>alle</td></tr>
<tr><td>V=vekt<br/>fast tall</td><td style="text-align: right">3</td><td style="text-align: right">2</td><td style="text-align: right">7</td><td style="text-align: right">6</td><td style="text-align: right">5</td><td style="text-align: right">4</td><td style="text-align: right">3</td><td style="text-align: right">2</td><td style="text-align: right">1</td><td>O*V<br/>↓</td></tr>
<tr><td>O*V</td><td style="text-align: right">24</td><td style="text-align: right">8</td><td style="text-align: right">14</td><td style="text-align: right">48</td><td style="text-align: right">35</td><td style="text-align: right">8</td><td style="text-align: right">0</td><td style="text-align: right">4</td><td style="text-align: right">2</td><td style="text-align: right">143</td></tr>
<tr><td colspan="11">Om kontrollsifferet er riktig avgjøres av om summen dividert på 11 gir et tall uten rest (et heltall).</td></tr>
<tr><td colspan="11">Dette er tilfelle her siden 143 / 11 = 13. Derfor har 842872022 gyldig kontrollsiffer og er høyst</td></tr>
<tr><td colspan="11">sannsynlig ikke en inntastingsfeil. Algoritmen kan nemlig gi ok selv om man taster feil, men det</td></tr>
<tr><td colspan="9">krever at minst to av sifrene er gale.</td><td colspan="2"></td></tr>
<tr><td colspan="9">9.3 Orgnr for utenlandske skoler</td><td colspan="2"></td></tr>
<tr><td colspan="11">For utenlandske skoler uten norsk orgnr benytter SO et såkalt fiktivt orgnr som starter på 4 (altså ikke</td></tr>
<tr><td colspan="11">8 eller 9 som de vanlige). Disse har likevel ni sifre og riktig kontrollsiffer. Pr 2014 har det blitt tildelt</td></tr>
<tr><td colspan="11">kun ett slikt. Kontakt SO (nvb-drift@samordnaopptak.no) for å få tildelt et slikt fiktivt orgnr for NVB.</td></tr>
<tr><td colspan="11">Merk at noen videregående skoler i utenlandet likevel har norske orgnr, søk i brreg.no for å eventuelt</td></tr>
<tr><td colspan="11">finne. NB: Slike fiktige orgnumre skal ikke spres slik at noen misforstår og tar de i bruk til andre</td></tr>
<tr><td colspan="9">formål.</td><td colspan="2"></td></tr>
<tr><td colspan="9">10 Versjonsnummer for kontroll.exe</td><td colspan="2"></td></tr>
<tr><td colspan="11">Kontroll.exe får et nytt versjonsnr hver år. I 2014 er vi kommet til versjon 15. For nye versjoner innen</td></tr>
<tr><td colspan="11">samme år økes 10-delsdesimalen. For mindre endringer som enten gjelder noen få skoler eller med</td></tr>
<tr><td colspan="11">mindre betydningsfulle endringer som ikke trenger å lastes ned av alle økes 100-delsdesimalen.</td></tr>
<tr><td colspan="11">For å angi at det er en tidlig eksperimentell versjon (alfaversjon) kan det settes en a bak versjons-</td></tr>
<tr><td colspan="11">nummeret. En nesten endelig versjon (betaversjon) kan være angitt med en b bak versjonsnummeret.</td></tr>
<tr><td colspan="9">11 Operativsystem og annen teknisk info</td><td colspan="2"></td></tr>
<tr><td colspan="9">Kontroll.exe kjører på Windows.</td><td colspan="2"></td></tr>
<tr><td colspan="9"></td><td style="text-align: right" colspan="2">30</td></tr>
</table>

<table class="table table-striped table-bordered"><tr><td>Men for de 12 som vil kjøre kontroll.exe på Linux legges det i tillegg ut en ut en Linux-binærfil i hver</td></tr>
<tr><td>versjon. Alternativt kan man kjøre kontroll.exe (windows-binærfilen) i Linux via Wine, en test viste</td></tr>
<tr><td>at det var mulig.</td></tr>
<tr><td>Kontrollmotoren er skrevet i Perl 5.8 og skal i prinsippet kunne kjøres på alle operativsystemer der</td></tr>
<tr><td>Perl kan installeres. Dvs de fleste.</td></tr>
<tr><td>Den er utviklet i og testet på Linux og deretter ”kompilert” til kjørbare Windows- og Linux-binærfiler</td></tr>
<tr><td>med programvaren PerlApp fra ActiveState.com.</td></tr>
<tr><td>Fagkontrolldelen av kontrollmotoren finnes også i en PL/SQL-modul for Oracle som kan være<br/>tilgjengelig ved forespørsel. Denne kjører internt hos SO (i tillegg til perl-varianten) samt hos</td></tr>
<tr><td>universitets- og høgskolesystemene FS (Felles studentsystem) og MSTAS fra IST.</td></tr>
<tr><td>12TPSYS har kjørt på dette på Linux siden ca 2012</td></tr>
<tr><td style="text-align: right">31</td></tr>
</table>

