# IMBOR-SW ontwikkelen op basis van het GWSW

Deze repository bevat het ontwerp voor de directe aanmaak van het deelmodel IMBOR Stedelijk Water vanuit het GWSW-datamodel .

Het ontwerp omvat het volgende:
* Definiëren van het deelmodel IMBOR-SW binnen het GWSW-datamodel (#gwsw-deelmodel-imbor-sw)
* Definieer de mapping GWSW-IMBOR, overnemen TOP-concepten uit IMBOR in het GWSW-datamodel
* Functionele beschrijving van de conversie GWSW naar IMBOR-SW

## GWSW Deelmodel IMBOR-SW 
Indeling vergelijkbaar met deelmodel GWSW Basis: CoF-en TOP, BAS, MDS, HYD (excl. NLCS)  
Gebruik conformiteitsklasse 6.

Het deelmodel wordt voor presentaties gekoppeld aan IMBOR-Kern. 
Om te komen tot de presentatie op [data.gwsw.nl/imbor-sw](https://data.gwsw.nl/imbor-sw) worden de volgende stappen doorlopen:
- voer de mapping GWSW-IMBOR uit op het datamodel GWSW
- filter deze mapping op basis van deelmodel IMBOR-SW (Cof-en TOP, BAS, MDS, HYD en CFK 6)
- converteer deze GWSW-filtering naar het IMBOR-conforme RDF

## Mapping GWSW-IMBOR
De mappingfile met de vertaling van GWSW naar IMBOR in JSON-vorm [(bestand Mapping IMBOR.json)](./Mapping%20IMBOR.json) 
wordt aangemaakt vanuit Excel [(bestand Mapping GWSW IMBOR.xlsm)](./Mapping%20GWSW%20IMBOR.xlsm). 
De mappingfile wordt toegepast in repository StichtingRIONED/gwsw_worker. Repository gwsw_worker maakt de conversie van het deelmodel GWSW-IMBOR-SW naar de RDF-vorm van IMBOR.

De spreadsheet Mapping GWSW IMBOR.xlsm wordt gevuld:
- het blad IMBOR: Gebruik de query IMBOR Classes+Props.rq in de map queries. De query draait op IMBOR-Kern (zie map datamodellen). 
- het blad GWSW: met de SPARQL-query Klassen binnen GWSW-Lijsten en de query GWSW Collecties.rq in de map queries. De queries draaien op het deelmodel GWSW-IMBOR-SW. Gebruik GraphDB-repository GWSW_IMBOR-SW_v161 (zie map datamodellen voor de inhoud. 

In Excel markeert de kolom IMBOR-module de indeling van de concepten:
- TOP = kernmodel IMBOR
- SW = disciplinemodel GWSW
- ? = module-plek nog onzeker

Map GWSW-concepten vanuit Excel op basis van:
- label GWSW versus label IMBOR
- altLabel GWSW versus label IMBOR
- alleen concepten binnen IMBOR-module TOP en ?

## IMBOR-conform RDF
