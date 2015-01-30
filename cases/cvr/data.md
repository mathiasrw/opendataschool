##CVR data


> **Info**
For at gennemføre denne case kræves en solid erfaring med IT
De beskrevne trin vises også som videoer

Derfor skal der installeres en PostgreSQL database samt PgAdmin (grafisk brugergrænseflade til PostgreSQL) før man kan følge de næste trin.
Det letteste er, at bruge en installationspakke som kan hentes [her](http://www.enterprisedb.com/products-services-training/pgdownload#windows)


Når databasen og PgAdmin er installeret, kan de følgnede trin påbegyndes.

### Indlæsning af CVR data i Postgres databasen


1. Hent [CVR data]([data](/../data/cvr.html))
2. Opret en database med PgAdmin
3. Opret et skema med navnet cvr
4. Opret en tabel til indlæsning af CVR data


```sql
CREATE TABLE cvr.virksomheder
(
  modifikationsstatus character varying,
  cvrnr character varying,
  livsforloeb_startdato character varying,
  livsforloeb_ophoersdato character varying,
  ajourfoeringsdato character varying,
  reklamebeskyttelse character varying,
  navn_gyldigfra character varying,
  navn_tekst character varying,
  beliggenhedsadresse_gyldigfra character varying,
  beliggenhedsadresse_vejnavn character varying,
  beliggenhedsadresse_vejkode character varying,
  beliggenhedsadresse_husnummerfra character varying,
  beliggenhedsadresse_husnummertil character varying,
  beliggenhedsadresse_bogstavfra character varying,
  beliggenhedsadresse_bogstavtil character varying,
  beliggenhedsadresse_etage character varying,
  beliggenhedsadresse_sidedoer character varying,
  beliggenhedsadresse_postnr character varying,
  beliggenhedsadresse_postdistrikt character varying,
  beliggenhedsadresse_bynavn character varying,
  beliggenhedsadresse_kommune_kode character varying,
  beliggenhedsadresse_kommune_tekst character varying,
  beliggenhedsadresse_postboks character varying,
  beliggenhedsadresse_conavn character varying,
  beliggenhedsadresse_adressefritekst character varying,
  postadresse_gyldigfra character varying,
  postadresse_vejnavn character varying,
  postadresse_vejkode character varying,
  postadresse_husnummerfra character varying,
  postadresse_husnummertil character varying,
  postadresse_bogstavfra character varying,
  postadresse_bogstavtil character varying,
  postadresse_etage character varying,
  postadresse_sidedoer character varying,
  postadresse_postnr character varying,
  postadresse_postdistrikt character varying,
  postadresse_bynavn character varying,
  postadresse_kommune_kode character varying,
  postadresse_kommune_tekst character varying,
  postadresse_postboks character varying,
  postadresse_conavn character varying,
  postadresse_adressefritekst character varying,
  virksomhedsform_gyldigfra character varying,
  virksomhedsform_kode character varying,
  virksomhedsform_tekst character varying,
  virksomhedsform_ansvarligdataleverandoer character varying,
  hovedbranche_gyldigfra character varying,
  hovedbranche_kode character varying,
  hovedbranche_tekst character varying,
  bibranche1_gyldigfra character varying,
  bibranche1_kode character varying,
  bibranche1_tekst character varying,
  bibranche2_gyldigfra character varying,
  bibranche2_kode character varying,
  bibranche2_tekst character varying,
  bibranche3_gyldigfra character varying,
  bibranche3_kode character varying,
  bibranche3_tekst character varying,
  telefonnummer_gyldigfra character varying,
  telefonnummer_kontaktoplysning character varying,
  telefax_gyldigfra character varying,
  telefax_kontaktoplysning character varying,
  email_gyldigfra character varying,
  email_kontaktoplysning character varying,
  kreditoplysninger_gyldigfra character varying,
  kreditoplysninger_tekst character varying,
  aarsbeskaeftigelse_aar character varying,
  aarsbeskaeftigelse_antalansatte character varying,
  aarsbeskaeftigelse_antalansatteinterval character varying,
  aarsbeskaeftigelse_antalaarsvaerk character varying,
  aarsbeskaeftigelse_antalaarsvaerkinterval character varying,
  aarsbeskaeftigelse_antalinclejere character varying,
  aarsbeskaeftigelse_antalinclejereinterval character varying,
  kvartalsbeskaeftigelse_aar character varying,
  kvartalsbeskaeftigelse_kvartal character varying,
  kvartalsbeskaeftigelse_antalansatte character varying,
  kvartalsbeskaeftigelse_antalansatteinterval character varying,
  produktionsenheder character varying,
  deltagere character varying

  )
  WITH (
    OIDS=FALSE
    );
```

5. Indlæs data med [COPY funktionen](http://www.postgresql.org/docs/9.4/static/sql-copy.html)
Udskift stien til filen, så den passer til placeringen af din egen fil.
```sql
COPY cvr.virksomheder
FROM '/Users/mbj/Downloads/39247470_42355_20150123134947/39247470_42355_20150123134947_VIRKSOMHEDER.csv'
WITH DELIMITER ','
CSV HEADER;
```

6.Undersøg hvilken branche, der har flest virksomheder i hver kommune

```sql
SELECT t3.* FROM
(beliggenhedsadresse_kommune_kode as kom_kode,beliggenhedsadresse_kommune_tekst, hovedbranche_tekst, count(1) as antal
FROM cvr.virksomheder GROUP BY beliggenhedsadresse_kommune_tekst,beliggenhedsadresse_kommune_kode, hovedbranche_tekst
) t3
JOIN
(
  select beliggenhedsadresse_kommune_tekst, max(antal) as antal
  from
  (
    select beliggenhedsadresse_kommune_tekst, hovedbranche_tekst, count(1) as antal
    FROM cvr.virksomheder group by beliggenhedsadresse_kommune_tekst , hovedbranche_tekst
    ) t
    GROUP BY beliggenhedsadresse_kommune_tekst
    ) t2
    ON (t2.antal = t3.antal AND t2.beliggenhedsadresse_kommune_tekst = t3.beliggenhedsadresse_kommune_tekst)

    ORDER BY beliggenhedsadresse_kommune_tekst
```

Når vi kigger på resultatet af denne forespørgsel, kan vi se at i de fleste kommuner er den største branche en af følgende:

|Branchekode|
|--------------|
|Andre organisationer og foreninger i.a.n.|
|Uoplyst|
|Ikke-finansielle holdingselskaber|
|Udlejning af erhvervsejendomme|

Det kan der være mage årsager til. Det er nogle meget brede kategorier og muligvis er virksomhederne placeret i disse i mangel af andre dækkende kategorier. Vi vælger at fjerne disse fra vores liste. Desuden får vi flere resulater pr. kommune, hvis der er brancher med samme antal virksomheder. Vi vil kun have nøjagtigt +en branche pr kommune. Derfor skal vi sortere en fra. Vi vælger - helt ukritisk - at beholde den branchekode, som alfabetisk har det største forbogstav med [Max](http://www.postgresql.org/docs/9.4/static/functions-aggregate.html). Det kan gøres med følgende sql


```sql
SELECT t3.kom_kode,t3.beliggenhedsadresse_kommune_tekst,max(t3.hovedbranche_tekst),t3.antal

FROM
(select '0' || beliggenhedsadresse_kommune_kode as kom_kode, beliggenhedsadresse_kommune_tekst, hovedbranche_tekst, count(1) as antal
FROM cvr.virksomheder GROUP BY beliggenhedsadresse_kommune_tekst,beliggenhedsadresse_kommune_kode, hovedbranche_tekst
) t3
JOIN
(
  select beliggenhedsadresse_kommune_tekst, max(antal) as antal
  from
  (
    select beliggenhedsadresse_kommune_tekst, hovedbranche_tekst, count(1) as antal
    FROM cvr.virksomheder WHERE hovedbranche_tekst NOT IN ('Andre organisationer og foreninger i.a.n.','Uoplyst','Ikke-finansielle holdingselskaber','Udlejning af erhvervsejendomme') group by beliggenhedsadresse_kommune_tekst , hovedbranche_tekst
    ) t
    GROUP BY beliggenhedsadresse_kommune_tekst
    ) t2
    ON (t2.antal = t3.antal AND t2.beliggenhedsadresse_kommune_tekst = t3.beliggenhedsadresse_kommune_tekst)
    GROUP BY t3.kom_kode,t3.beliggenhedsadresse_kommune_tekst,t3.antal
    ORDER BY beliggenhedsadresse_kommune_tekst
```
Nu ligner resultatet noget, der kan give et billede af de fremherskende brancher i kommunerne. Vi eksporterer nu resultatet til CartoDB.

7.Marker SQL i PgAdmin og vælg i meuen: Query-> Execute to file. Kald filen top_brancher.csv
8.Log ind på CartoDB og opret ny tabel som i [CASE om momsdata]](/../cases/moms/data.html))
9. Tilret kom_kode feltet
Vi vil nu joine vores analyseresultat med vores kommunegrænser. Kommunekoden i cvr er et heltal mens kommunekoden i vores DAGI koommuner er et tekstfelt bestående at et foranstillet nul eferfulgt af kommunekoden.
Derfor tilføjer vi en kolonne til vores upladede datasæt (top_brancher). Vi åbner vores tabel i CartoDB og vælger SQL fanen og indtaster følgende sql, som opretter en ny kolonne til vores kommunekoder.


```sql
ALTER TABLE top_brancher ADD column kom_kode_ny character varying;

UPDATE top_brancher SET kom_kode_ny = '0' || kom_kode
```

Vi er nu klar til at lave vores JOIN af vores cvr analyseresultat og vores DAGI kommunegrænser. Vi vil benytte en funktion (merge tables) i CartoDB til at JOINE to tabeller. Se hvordan i videoen herunder
