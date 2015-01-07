## Data til momskortet

Til denne case anvendes dels data fra SKAT for momsomsætning fordelt på virksomheder og dels de kommunale administrative grænser fra Geodatastyrelsen. Se afsnit om [data](/../data/README.html)


##CartoDB

###Upload moms-data til CartoDB

1. Opret en konto hos [CartoDB](https://cartodb.com/)
2.Klik "view your tables"
3. Klik "New table" og vælg filen fra [Moms-data](/../data/moms.html)

###Upload kommunegrænser til CartoDB
1. Klik "view your tables"
2. Klik "New table" og vælg filen fra [DAGI-data](/../data/dagi.html)
3. Udpak filen og vælg alle filerne med navnet KOMMUNE.* og pak dem i en nye .zip fil
4. Upload denne nye zip fil med kommunegrænser til CartoDB

### Klargøring af data

Førend de to datasæt kan forenes (joines) korrekt, er det nødvendigt at de deler en fælles nøgle. I denne case skal data joines på kommune. DAGI datasætte med kommunegrænser indeholder en kolonne for kommunekoden og en kolonne for kommunenavnet mens moms-datasættet alene indeholder en kolonne for kommunenavn. Derfor er vi nødsaget til at bruge kolonnen for kommunenavn til vores data-join. Derudover bruges ikke nøjagtigt samme kommunenavne i de to datasæt. Vi vil derfor forsøge at manipulere data således der er to kolonner med samme stavemåde på kommunenavne.

> **Caution**
Det er ikke optimalt at joine på et tekstfelt. En kolonne med kommunekode bestående af heltal vil være meget bedre at anvende til at joine de to datasæt.


#### Kommunenavne

Da CartoDB er baseret på [SQL](http://da.wikipedia.org/wiki/Structured_Query_Language), vil vi manipulere data mes SQL statements. Vi vil oprette en ny kolonne i vores datasæt for kommunegrænser som stemmer overnes med dén navngivning af kommunerne, der findes i moms-datasættet.

1. Klik på SQL fanen i tabelvisningen af kommunegrænser
2. Opret ny kolonne med følgende SQL

```sql
ALTER TABLE kommune_union ADD column komnavn_moms character varying
```
Opdatér kolonnen med de eksisterende kommunenavne

```sql
UPDATE kommune_union SET komnavn_moms = komnavn || ' Kommue'
```

Nu har vi en kopi af kolonnen vi kan arbejde videre med. I moms-datasættet kan vi se, at alle kommunenavnene har endelsen "Kommune". Vi starter med at tilføjde denne endelser i vores nye kolonne.






```sql
WITH moms
AS (SELECT omsaetning,
  branche,
  replace(kommune, ' Kommune', '') AS kommune
  FROM   skat_momsomsaetning_2012),
  kom
  AS (SELECT cartodb_id,
    the_geom_webmercator,
    komnavn
    FROM   kommune_union)
    SELECT kom.cartodb_id,
    kom.komnavn,
    kom.the_geom_webmercator,
    moms.kommune,
    moms.omsaetning,
    moms.branche
    FROM   kom
    JOIN moms
    ON ( kom.komnavn = moms.kommune
      AND moms.branche= 'Murere')
      ORDER BY moms.kommune DESC
```
