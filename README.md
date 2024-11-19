# IMBOR-SW ontwikkelen op basis van het GWSW

Directe aanmaak vanuit het GWSW van het deelmodel IMBOR Stedelijk Water.

* Definiëren van het deelmodel IMBOR-SW
* Overnemen TOP-concepten uit IMBOR in het GWSW Datamodel
* Functionele beschrijving van de conversie GWSW naar IMBOR-SW

De mappingtabel met de vertaling van GWSW naar IMBOR (in JSON-vorm (bestand Mapping IMBOR.json) wordt aangemaakt vanuit Excel (bestand Mapping GWSW IMBOR.xlsm). 
De mappingtabel wordt toegepast in repos StichtingRIONED/gwsw_worker. Repos gwsw_worker maakt de conversie van het deelmodel GWSW-IMBOR-SW naar de RDF-vorm van IMBOR.

In Excel markeert de kolom IMBOR-module de indeling van de concepten:
- TOP = kernmodel IMBOR
- SW = disciplinemodel GWSW
- ? = module-plek nog onzeker

Map GWSW-concepten vanuit Excel op basis van:
- label GWSW versus label IMBOR
- altLabel GWSW versus label IMBOR
- alleen concepten binnen IMBOR-modules TOP en ?

## GWSW Deelmodel IMBOR-SW 
Indeling vergelijkbaar met deelmodel GWSW: CoF-en TOP, BAS, MDS, HYD (excl. NLCS)  
Gebruik conformiteitsklasse 6:
- schakel activiteiten uit (komen niet voor in IMBOR)


