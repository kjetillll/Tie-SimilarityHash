
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
