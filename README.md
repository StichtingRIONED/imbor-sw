# IMBOR-SW ontwikkelen op basis van het GWSW

Deze repository bevat het ontwerp voor de directe aanmaak van het deelmodel IMBOR Stedelijk Water vanuit het GWSW-datamodel .

Het ontwerp omvat het volgende:
* Definiëren van het deelmodel IMBOR-SW binnen het GWSW-datamodel [GWSW Deelmodel IMBOR-SW](#gwsw-deelmodel-imbor-sw)
* Definieer de mapping GWSW-IMBOR, overnemen TOP-concepten uit IMBOR in het GWSW-datamodel [Mapping GWSW-IMBOR](#mapping-gwsw-imbor)
* Functionele beschrijving van de conversie GWSW naar IMBOR-SW [IMBOR in RDF](#imbor-in-rdf)

## GWSW Deelmodel IMBOR-SW 

Zie [data.gwsw.nl/imbor-sw](https://data.gwsw.nl/imbor-sw).  

Indeling (vergelijkbaar met deelmodel GWSW Basis): CoF-en TOP, BAS, MDS, HYD (excl. NLCS). Gebruik conformiteitsklasse 6.

Om te komen tot de presentatie op [data.gwsw.nl/imbor-sw](https://data.gwsw.nl/imbor-sw) worden de volgende stappen doorlopen:
- converteer het model IMBOR-Kern naar het GWSW-conforme RDF 
- koppel het omgezette model IMBOR-Kern aan het datamodel GWSW
- voer de mapping GWSW-IMBOR uit op deze model-koppeling
- filter deze mapping op basis van deelmodel IMBOR-SW (Cof-en TOP, BAS, MDS, HYD en CFK 6)

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

## IMBOR in RDF

### SW-deel (omgezet vanuit GWSW)

In de volgende paragrafen is als voorbeeld een datamodel IMBOR-conform uitgewerkt.  
In dit voorbeeld zijn enkele omgezette concepten van GWSW en bijbehorende topconcepten van IMBOR-Kern opgenomen.

Gemodelleerd zijn de fysieke objecten **rioolput** en **overstortput** inclusief enkele **kenmerken**.

Het test-datamodel in de GWSW/RDF-viewer: [data.gwsw.nl/imbor-sw-test](https://data.gwsw.nl/1.6.1/imbor-sw-test).  
Het test-datamodel in RDF/Turtle-vorm: [datamodel imbor-sw-test](./datamodel%20imbor-sw-test.ttl).

**SW prefixes**
* gwsw: voor ongewijzigde SW-concepten
* imbor-sw: voor IMBOR-conforme toevoegingen (propertyshapes, kenmerk-attributen), in URI geen guid maar SW-namen

### Klassen

Voorbeeld Rioolput en Overstortput

<pre>
gwsw:Rioolput
        rdf:type         rdfs:Class , sh:NodeShape ;
        rdfs:seeAlso     imbor-term:100c3a15-3342-46fd-9f48-843c076571e9 ; # verwijst naar vocabulaire-model
        rdfs:subClassOf  imbor:b7168388-9eb9-4c95-b35e-1ba2660849e8 ; # verwijst naar IMBOR-concept Put (neutrale/taalonafhankelijke identificatie)
        skos:definition  "Constructie toegang gevend tot het rioolstelsel."@nl  ;
        skos:prefLabel   "Rioolput"@nl  ;
        sh:property      imbor:0579a2c5-bff7-417f-860c-8299a806c419 ; # verwijst naar onderscheidend kenmerk "Functie"
        sh:property      imbor:00910ff5-f2c7-4f22-ad64-787552e3dd38 ; # verwijst naar referentie "Maaiveldschematisering"
        sh:property      imbor:b6ac17af-8019-4d03-9a72-096b51b0cebe ; # verwijst naar waarde "LengteToegang"
        sh:property      imbor:7ad9fafd-6eb1-4853-9b23-2e8f0e7003d4 . # verwijst naar waarde "Aantal i.e. recreatie-eenheden."
        # enzovoort: totaal 62 objects bij sh:property
gwsw:Overstortput
        rdf:type         rdfs:Class , sh:NodeShape ;
        rdfs:subClassOf  gwsw:Rioolput ;
        sh:property      imbor:8e3ccb03-a015-420d-a646-bfd05ae734bb . # verwijst naar relatie "hasPart"
</pre>

### Abstracte klassen

<pre>
imbor-sw:CfkTypes
        a                 sh:NodeShape ;
        sh:targetNode     gwsw:Rioolput, gwsw:Leiding ;
        sh:property       [
          sh:path         [ sh:inversePath rdf:type ] ;
          sh:maxCount     0 ;
        ] .
</pre>

>Gebruik een SHACL construct ipv dash:abstract, deze expliciete variant is nodig voor validatie.

### Onderscheidend kenmerk 

Functie in IMBOR-vorm

<pre>
imbor:0579a2c5-bff7-417f-860c-8299a806c419
        rdf:type                sh:PropertyShape ;
        skos:prefLabel          "Rioolput voert uit Leidingen aansluiten"@nl ;
        sh:path                 nen2660:executes ; # verwijst naar de rdf:Property (analoog aan gwsw:functie)
        sh:qualifiedMaxCount    1 ;
        sh:qualifiedMinCount    0 ;
        sh:qualifiedValueShape  [ sh:class  imbor:8a328d2a-120f-4fd7-a3ab-eed6a61c2539 ] . # verwijst naar de (functie) klasse "Leidingen aansluiten"
</pre>

>IMBOR hanteert functie voor lokale definities. Daarmee kan een IMBOR-object lokaal een andere betekenis krijgen. 
Dat is principieel heel anders dan het GWSW, daar is een eenduidige definitie/betekenis bij de objectnaam een voorwaarde. 
Afgesproken om in de omzetter de IMBOR-syntax op te nemen, mogelijk met sh:qualifiedMinCount 1?

>Nog bepalen of andere onderscheidende kenmerken (gwsw:Uitvoering) meegeleverd worden via de prop gwsw:uitvoering

### Relatie hasPart in IMBOR-vorm

<pre>
imbor-sw:Overstortput_Duikschot_card
        rdf:type                sh:PropertyShape ;
        skos:prefLabel          "Overstortput heeft deel Duikschot"@nl ;
        sh:path                 nen2660:hasPart ; # verwijst naar de rdf:Property
        sh:qualifiedMaxCount    1 ;
        sh:qualifiedValueShape  [ sh:class  gwsw:Duikschot ] .
</pre>

>IMBOR kent ook de inverse relatie nen2660:isPartOf (is ook nodig voor kardinaliteits-definitie)

### Kenmerk - hasReference in IMBOR-vorm

<pre>
imbor-sw:Rioolput_Maaiveldschematisering_card
        rdf:type                sh:PropertyShape ;
        skos:prefLabel          "maaiveldschematisering vastgezet op Rioolput"@nl ;
        sh:path                 imbor:f4c31d93-4ed7-47f2-8b91-a24247de7e3d ; # is type rdf:Property
        sh:qualifiedMaxCount    1 ;
        sh:qualifiedValueShape  [ sh:class  gwsw:Maaiveldschematisering ] .
</pre>

>**SW-Specifiek**
>* in propertyshapes geen skos:prefLabel
>* wel sh:message en sh:severity opgenomen
>* gescheiden propershapes (met eigen message en severity) voor datatype en kardinaliteit

En de property als object van sh:path:

<pre>
imbor:f4c31d93-4ed7-47f2-8b91-a24247de7e3d
        rdf:type         rdf:Property ;
        rdfs:seeAlso     imbor-term:8e33d96c-1640-4edc-9123-87448cb9176f ;
        skos:definition  "Aanduiding schematisering maaiveld bij uitstroom van water via de putdeksel."@nl ;
        skos:note        "Enumeratie"@nl ;
        skos:prefLabel   "maaiveldschematisering"@nl .
</pre>

En de enumeratie: "Gekneveld" is een individu van type "RioolputMaaiveldschematisering"

<pre>
imbor:90b87700-9800-4394-89d5-377291331901 # De klasse "RioolputMaaiveldschematisering"
        rdf:type         owl:Class ;
        rdfs:seeAlso     imbor-term:2dfe6c5a-74cf-48a5-9f53-97f1cf50d655 ;
        rdfs:subClassOf  nen2660:EnumerationType ;
        skos:definition  "Het enumeratietype RioolputMaaiveldschematisering is de naam voor de domeinwaardenlijst van de klasse Rioolput en het attribuut maaiveldschematisering."@nl ;
        skos:prefLabel   "RioolputMaaiveldschematisering"@nl ;
        imbor:typeLijst  "Enumeratielijst"@nl .

imbor-domeinwaarde:184c2d2e-6990-4b84-a334-875dc4c0b09b # Het individu "Gekneveld"
        rdf:type         imbor:90b87700-9800-4394-89d5-377291331901 ;
        rdfs:seeAlso     imbor-term:90139b60-08e5-4dd8-8899-0af7882c6505 ;
        skos:definition  "Een maaiveldschematisering die op een bepaalde manier gesloten is met knevels, dat wil zeggen met voorzieningen bedoeld om een afdekking af te dichten."@nl ;
        skos:prefLabel   "Gekneveld"@nl .

imbor:90b87700-9800-4394-89d5-377291331901-list-0 # Maak een lijst met individuen
        rdf:type   rdf:List ;
        rdf:first  imbor-domeinwaarde:184c2d2e-6990-4b84-a334-875dc4c0b09b ;
        rdf:rest   imbor:90b87700-9800-4394-89d5-377291331901-list-1 .

imbor:00910ff5-f2c7-4f22-ad64-787552e3dd38 # de PropertyShape, zie "Kenmerk - hasReference in IMBOR-vorm"
        sh:in   imbor:90b87700-9800-4394-89d5-377291331901-list-0 .
</pre>

>sh:qualifiedValueShape staat in IMBOR als achtervang, voor het geval het individu niet voorkomt. 
In IMBOR-SW is sh:in voldoende (SW-individuen zijn er altijd)

### Kenmerk - hasAspect in IMBOR-vorm
<pre>
imbor:7ad9fafd-6eb1-4853-9b23-2e8f0e7003d4
        rdf:type            sh:PropertyShape ;
        skos:prefLabel      "aantal i.e. recreatie vastgezet op Rioolput"@nl ;
        sh:datatype         xsd:positiveInteger ;
        sh:maxCount         1 ;
        sh:minCount         0 ;
        sh:path             imbor:ffb7311e-ade9-46e4-bd4a-4eec7ce10789 ; # verwijst naar de rdf:Property
</pre>

En de property als object van sh:path:
<pre>
imbor:ffb7311e-ade9-46e4-bd4a-4eec7ce10789
        rdf:type         rdf:Property ;
        rdfs:seeAlso     imbor-term:c3f45eea-738c-4a37-906b-d872192e0c08 ;
        skos:definition  "Aantal i.e. recreatie-eenheden."@nl ;
        skos:note        "Vrij invoerveld"@nl ;
        skos:prefLabel   "aantal i.e. recreatie"@nl .
</pre>

>**SW-Specifiek**
>* conform voorbeeld, behalve skos:note

## Vocabulaire IMBOR

Bij Rioolput verwijst "rdfs:seeAlso  imbor-term:100c3a15-3342-46fd-9f48-843c076571e9" naar het vocabulaire-concept

<pre>
imbor-term:100c3a15-3342-46fd-9f48-843c076571e9
        rdf:type         skos:Concept ;
        skos:broader     imbor-term:291e6f76-66af-4c3c-b37f-12c4f5675429 ; # Verwijst naar supertype "Put"
        skos:definition  "Constructie toegang gevend tot het rioolstelsel."@nl ;
        skos:inScheme    imbor-term:term-schema ;
        skos:prefLabel   "Rioolput"@nl .
</pre>

>**SW-Specifiek**
>* conform voorbeeld, behalve skos:inScheme
