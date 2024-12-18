# IMBOR-SW ontwikkelen op basis van het GWSW

Deze repository bevat het ontwerp voor de directe aanmaak van het deelmodel IMBOR Stedelijk Water vanuit het GWSW-datamodel .

Zie [data.gwsw.nl/imbor-sw](https://data.gwsw.nl/1.6.1/imbor-sw/index.html?menu_item=classes).  
In kleur de gebruikte RDF-schema's <span style="color: blue;">blauw=IMBOR-Kern</span>, <span style="color: orangered;">rood=NEN2660</span>

Het ontwerp omvat het volgende:
* Definiëren van het deelmodel IMBOR-SW binnen het GWSW-datamodel [GWSW Deelmodel IMBOR-SW](#gwsw-deelmodel-imbor-sw)
* Maak de mapping GWSW-IMBOR, overnemen TOP-concepten uit IMBOR in het GWSW-datamodel [Mapping GWSW-IMBOR](#mapping-gwsw-imbor)
* Functionele beschrijving van de conversie GWSW naar IMBOR-SW [GWSW in IMBOR-vorm](#gwsw-in-imbor-vorm)

## GWSW Deelmodel IMBOR-SW 

Indeling (vergelijkbaar met deelmodel GWSW Basis): CoF-en TOP, BAS, MDS, HYD (excl. NLCS). Gebruik conformiteitsklasse 6.

Om te komen tot de presentatie op [data.gwsw.nl/imbor-sw](https://data.gwsw.nl/1.6.1/imbor-sw/index.html?menu_item=classes) worden de volgende stappen doorlopen:
- converteer het model IMBOR-Kern naar het GWSW-OroX protocol (GWSW-conform RDF)
- koppel het omgezette model IMBOR-Kern aan het datamodel GWSW
- voer de mapping GWSW-IMBOR uit op deze model-koppeling
- filter deze mapping op basis van deelmodel IMBOR-SW (Cof-en TOP, BAS, MDS, HYD en CFK 6)
- de presentatie (via DivOro.exe) is afgestemd op het IMBOR-SW, gebruikt de mapping en markeert de gebruikte schema's (NEN2660, IMBOR enz).

## Mapping GWSW-IMBOR
De mappingfile met de vertaling van GWSW naar IMBOR in JSON-vorm [(bestand Mapping GWSW-IMBOR.json)](./Mapping%20GWSW-IMBOR.json) 
wordt aangemaakt vanuit Excel [(bestand Mapping GWSW-IMBOR.xlsm)](./Mapping%20GWSW-IMBOR.xlsm). 
De mappingfile wordt toegepast in repository StichtingRIONED/gwsw_worker.
De app in repository gwsw_worker maakt de conversie van het deelmodel GWSW-IMBOR-SW naar de RDF-vorm van IMBOR.

De spreadsheet Mapping GWSW IMBOR.xlsm wordt gevuld:
- vervallen (202412150): het blad IMBOR: Gebruik de query IMBOR Classes+Props.rq in de map queries. De query draait op IMBOR-Kern (zie map datamodellen).
- het blad IMBORVoc_Termen: bevat de termen-tabel vanuit MS-Access.
- het blad GWSW: met de SPARQL-query Klassen binnen GWSW-Lijsten en de query GWSW Collecties.rq in de map queries. 

>Gegevensbron blad GWSW
>De queries draaien op het deelmodel GWSW-IMBOR-SW. Gebruik GraphDB-repository GWSW_IMBOR-SW_v161 (zie map datamodellen voor de inhoud).
De datamodel GWSW_IMBOR-SW_v161 is het filter op GWSW-IMBOR-SW exclusief het IMBOR-Kern datamodel, denk aan de parameter in app.config.json
(gebruik voor presentaties van GWSW-IMBOR-SW wel het IMBOR-Kern model).

In Excel markeert de kolom IMBOR-module de indeling van de concepten:
- TOP = kernmodel IMBOR
- SW = disciplinemodel GWSW
- ? = module-plek nog in overleg

Map GWSW-concepten vanuit Excel op basis van:
- label GWSW versus label IMBOR
- altLabel GWSW versus label IMBOR
- alleen concepten binnen IMBOR-module TOP en ?

## GWSW in IMBOR-vorm

### Algemene modelleerprincipes IMBOR

Externe datamodellen kunnen **annotaties** toevoegen aan de concepten in IMBOR-Kern (zie Installatiedatum).
Zoals comments over niet gebruikte maar wel geërfde IMBOR-Kern-properties of over uitwisselingsvorm (verwijzing naar GWSW-OroX).
Bijvoorbeeld:
<pre>
imbor:f65dd67b-8c4c-47f6-9ed2-4ab87763bbac rdfs:comment "[GWSW-OroX] gwsw:hasApect gwsw:Begindatum"@nl . # label in IMBOR-Kern = Installatiedatum
</pre>

Externe datamodellen kunnen **properties** toevoegen aan de concepten in IMBOR-Kern.  
Bijvoorbeeld (zie [data.gwsw.nl/imbor-sw/einddatum](https://data.gwsw.nl/1.6.1/imbor-sw/index.html?menu_item=classes&item=../../def/1.6.1/IMBOR-SW/Einddatum)):

<pre>
imbor-sw:einddatum                     rdf:type           rdf:Property ;
                                       rdfs:subPropertyOf imbor-sw:107332e5-5843-46b7-901d-13633fba7ef7 . # label in IMBOR-Kern = datum
imbor-sw:DiscreteObject_Einddatum_card rdf:type           sh:PropertyShape;
    sh:path imbor-sw:einddatum;
    sh:qualifiedMaxCount 1;
    sh:message "Subject DiscreteObject, path imbor-sw:einddatum - aantal voorkomens wijkt af (max=1)"@nl;
    sh:severity sh:Violation.
</pre>

Externe datamodellen kunnen **relaties** toevoegen aan de concepten in IMBOR-Kern. 
Bijvoorbeeld (zie [data.gwsw.nl/imbor-sw/Stellaag](https://data.gwsw.nl/1.6.1/imbor-sw/index.html?menu_item=classes&item=../../def/1.6.1/IMBOR-SW/Stellaag)),
Stellaag is deel van Put:

<pre>
imbor-sw:b7168388-9eb9-4c95-b35e-1ba2660849e8_Stellaag_card rdf:type sh:PropertyShape;
    sh:path [
      sh:alternativePath (nen2660:hasPart [ sh:inversePath nen2660:isPartOf ])
    ];
    sh:qualifiedValueShape [ sh:class gwsw:Stellaag ];
    sh:qualifiedMaxCount 1;
    sh:message "Subject b7168388-9eb9-4c95-b35e-1ba2660849e8, path hasPart, object Stellaag - aantal voorkomens wijkt af (max=1)"@nl;
    sh:severity sh:Violation.
</pre>

Externe datamodellen kunnen **subproperties** toevoegen aan de concepten in IMBOR-Kern.
Bijvoorbeeld (zie [data.gwsw.nl/imbor-sw/aanlegdiepte](https://data.gwsw.nl/1.6.1/imbor-sw/index.html?menu_item=classes&item=../../def/1.6.1/IMBOR-SW/Aanlegdiepte)).
<pre>
imbor-sw:aanlegdiepte rdf:type           rdf:Property ; 
                      rdfs:subPropertyOf imbor-sw:0dac7828-b6e7-46eb-b538-dd6ac0005eca . # label in IMBOR-Kern = eendimensionale eigenschap 
</pre>

### Deelmodel GWSW-IMBOR-SW omgezet in IMBOR-vorm

In de volgende paragrafen is als voorbeeld een datamodel IMBOR-conform uitgewerkt, het voorbeeld bevat enkele omgezette concepten van GWSW en bijbehorende topconcepten van IMBOR-Kern.

Gemodelleerd zijn de fysieke objecten **rioolput** en **overstortput** inclusief enkele **kenmerken**.
Zie ook het test-datamodel in de GWSW/RDF-viewer: [data.gwsw.nl/imbor-sw-test](https://data.gwsw.nl/1.6.1/imbor-sw-test/index.html?menu_item=classes).   

Voor koppeling met IMBOR-Kern:
>Het test-datamodel in RDF/Turtle-vorm (alleen IMBOR-SW): [datamodel imbor-sw-test](./data%20models%20target/datamodel%20imbor-sw-test.ttl).  
>De test-vocabulaire in RDF/Turtle-vorm (alleen IMBOR-SW): [vocabulaire imbor-sw-test](./data%20models%20target/vocabulaire%20imbor-sw-test.ttl).  

Ter informatie en voor proefnemingen:
>Het test-datamodel in RDF/Turtle-vorm inclusief IMBOR-Kern: [datamodel imbor-sw-test inclusief Kern](./data%20models%20target/datamodel%20imbor-sw-test%20inclusief%20kern.ttl).   
>Een test-dataset in RDF/Turtle-vorm (voor SHACL-test): [dataset imbor-sw-test](./data%20models%20target/dataset%20imbor-sw-test.ttl).

**SW prefixes**
* gwsw: voor ongewijzigde SW-concepten
* imbor-sw: voor IMBOR-conforme toevoegingen van SW-concepten (propertyshapes, kenmerk-attributen)

### Klassen

Voorbeeld Rioolput en Overstortput

<pre>
gwsw:Rioolput
        rdf:type         rdfs:Class, sh:NodeShape ;
        rdfs:seeAlso     imbor-term:100c3a15-3342-46fd-9f48-843c076571e9 ; # verwijst naar vocabulaire-model
        rdfs:subClassOf  imbor:b7168388-9eb9-4c95-b35e-1ba2660849e8 ; # verwijst naar IMBOR-concept Put (neutrale/taalonafhankelijke identificatie)
        skos:definition  "Constructie toegang gevend tot het rioolstelsel."@nl  ;
        skos:prefLabel   "Rioolput"@nl  ;
        sh:property      imbor-sw:Rioolput_Functie_LeidingenAansluiten ; # zie volgende paragrafen
        sh:property      imbor-sw:Rioolput_Maaiveldschematisering ; # zie volgende paragrafen
        sh:property      imbor-sw:Rioolput_Aantal_ieRecreatie . # zie volgende paragrafen
        # enzovoort: totaal 62 objects bij sh:property
gwsw:Overstortput
        rdf:type         rdfs:Class , sh:NodeShape ;
        rdfs:subClassOf  gwsw:Rioolput ;
        sh:property      imbor:8e3ccb03-a015-420d-a646-bfd05ae734bb . # verwijst naar relatie "hasPart"
</pre>

De propertyshapes (object bij sh:property) worden in de volgende paragrafen uitgewerkt.

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

>Gebruik een SHACL construct ipv dash:abstract, deze expliciete variant is bruikbaar voor validatie.

### Onderscheidend kenmerk in IMBOR-vorm (functie, voert uit)

Functie in IMBOR-vorm

<pre>
imbor-sw:Rioolput_Functie_LeidingenAansluiten
        rdf:type                sh:PropertyShape ;
        skos:prefLabel          "Rioolput voert uit Leidingen aansluiten"@nl ;
        sh:path                 nen2660:executes ; # verwijst naar de rdf:Property (analoog aan gwsw:functie)
        sh:qualifiedMaxCount    1 ;
        sh:qualifiedMinCount    0 ;
        sh:qualifiedValueShape  [ sh:class  gwsw:LeidingenAansluiten ] . # verwijst naar de (functie) klasse "Leidingen aansluiten"
</pre>

>IMBOR hanteert functie voor lokale definities. Daarmee kan een IMBOR-object lokaal een andere betekenis krijgen. 
Dat is principieel heel anders dan het GWSW, daar is een eenduidige definitie/betekenis bij de objectnaam een voorwaarde.

>**SW-Specifiek**
>* sh:qualifiedCount 1, de functie is onderdeel van de rioolput-definitie 

En de definitie van de functie-klasse

<pre>
gwsw:LeidingenAansluiten a rdfs:Class, sh:NodeShape;
        dash:abstract           false;
        rdfs:seeAlso            imbor-term:f584ecc4-8c48-4494-b75d-fbbf3901be13;
        rdfs:subClassOf         sml:Function;
        skos:definition         "Het object wordt gebruikt om leidingen aan te sluiten."@nl;
        skos:prefLabel          "Leidingen aansluiten"@nl.
</pre>

>In de NEN2660 is de relatie nen2660:executes gedefinieerd, die is vergelijkbaar met gwsw:functie.
Van de andere onderscheidende kenmerken binnen het GWSW (gwsw:uitvoering, gwsw:mechanisme, ...) is nog geen vergelijkbare relatie in de NEN26660 opgenomen.

>Het GWSW-concept gwsw:LeidingenAansluiten is een individu (van het type gwsw:Functie) en wordt binnen IMBOR als class gedefinieerd.

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

### Kenmerk - hasAspect in IMBOR-vorm (kwantitatief attribuut)
<pre>
imbor-sw:Rioolput_Aantal_ieRecreatie
        rdf:type            sh:PropertyShape ;
        skos:prefLabel      "aantal i.e. recreatie vastgezet op Rioolput"@nl ;
        sh:datatype         xsd:positiveInteger ;
        sh:maxCount         1 ; # kardinaliteit
        sh:minCount         0 ;
        sh:path             imbor-sw:aantal_ieRecreatie ; # verwijst naar de rdf:Property
</pre>

En de property als object van sh:path:
<pre>
imbor-sw:aantal_ieRecreatie
        rdf:type         rdf:Property ;
        rdfs:seeAlso     imbor-term:c3f45eea-738c-4a37-906b-d872192e0c08 ;
        skos:definition  "Aantal i.e. recreatie-eenheden."@nl ;
        skos:note        "Vrij invoerveld"@nl ;
        skos:prefLabel   "aantal i.e. recreatie"@nl .
</pre>

>**SW-Specifiek**
>* in propertyshapes geen skos:prefLabel
>* wel sh:message en sh:severity opgenomen
>* gescheiden propershapes (met eigen message en severity) voor datatype en kardinaliteit
>* geen skos:note in property

### Kenmerk - hasReference in IMBOR-vorm (kwalitatief attribuut, enumeratie)

<pre>
imbor-sw:Rioolput_Maaiveldschematisering
        rdf:type                sh:PropertyShape ;
        skos:prefLabel          "maaiveldschematisering vastgezet op Rioolput"@nl ;
        sh:path                 imbor-sw:maaiveldschematisering ; # is type rdf:Property
        sh:qualifiedMaxCount    1 ;
        sh:qualifiedValueShape  [ sh:class  imbor-sw:MaaiveldschematiseringColl ] .
</pre>

En de property als object van sh:path:

<pre>
imbor-sw:maaiveldschematisering
        rdf:type         rdf:Property ;
        rdfs:seeAlso     imbor-term:8e33d96c-1640-4edc-9123-87448cb9176f ;
        skos:definition  "Aanduiding schematisering maaiveld bij uitstroom van water via de putdeksel."@nl ;
        skos:note        "Enumeratie"@nl ;
        skos:prefLabel   "maaiveldschematisering"@nl .
</pre>

>**SW-Specifiek**
>* in propertyshapes geen skos:prefLabel
>* wel sh:message en sh:severity opgenomen
>* gescheiden propershapes (met eigen message en severity) voor datatype en kardinaliteit
>* geen skos:note in property

En de enumeratie: "Gekneveld" is een individu van type "RioolputMaaiveldschematisering"

<pre>
imbor-sw:MaaiveldschematiseringColl # De collectie-klasse
        rdf:type         rdfs:Class ;
        rdfs:seeAlso     imbor-term:2dfe6c5a-74cf-48a5-9f53-97f1cf50d655 ;
        rdfs:subClassOf  nen2660:EnumerationType ;
        skos:definition  "Het enumeratietype RioolputMaaiveldschematisering is de naam voor de domeinwaardenlijst van de klasse Rioolput en het attribuut maaiveldschematisering."@nl ;
        skos:prefLabel   "Maaiveldschematisering (coll)"@nl ;
        imbor:typeLijst  "Enumeratielijst"@nl .

imbor-domeinwaarde:184c2d2e-6990-4b84-a334-875dc4c0b09b # Het individu "Gekneveld"
        rdf:type         imbor:MaaiveldschematiseringColl ;
        rdfs:seeAlso     imbor-term:90139b60-08e5-4dd8-8899-0af7882c6505 ;
        skos:definition  "Een maaiveldschematisering die op een bepaalde manier gesloten is met knevels, dat wil zeggen met voorzieningen bedoeld om een afdekking af te dichten."@nl ;
        skos:prefLabel   "Gekneveld"@nl .

imbor:MaaiveldschematiseringColl-list-0 # Maak een lijst met individuen
        rdf:type   rdf:List ;
        rdf:first  imbor-domeinwaarde:184c2d2e-6990-4b84-a334-875dc4c0b09b ; # het collectie-element (individu)
        rdf:rest   imbor:MaaiveldschematiseringColl-list-1 . 
# Enzovoort, sluit af met
        rdf:rest rdf:nil .

imbor-sw:Rioolput_Maaiveldschematisering # de PropertyShape
        sh:in   imbor:MaaiveldschematiseringColl-list-0 .
</pre>

>sh:qualifiedValueShape staat in IMBOR als achtervang, voor het geval het individu niet voorkomt. 
In IMBOR-SW is sh:in voldoende (SW-individuen zijn er altijd)

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
