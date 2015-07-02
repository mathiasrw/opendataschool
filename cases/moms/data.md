# Data til momskortet

Til denne case anvendes dels data fra SKAT for momsomsætning fordelt på virksomheder og dels de kommunale administrative grænser fra Geodatastyrelsen. Se afsnit om [data](http://virkdata.gitbooks.io/open-data-school/content/data/index.html)

#CartoDB

I denne CASE bruger vi CartoDB som er baseret på en [PostgreSQL](http://www.postgresql.org/) database med funktionalitet til geografiske data og analyser med udvidelsen [PostGIS](http://postgis.net/). Når data er indlæst i CartoDB, kan vi dermed bruge SQL til at udforske vores datasæt samt manipulere vores data i databasen. CartoDB er udstyret med en SQL editor direkte i webbrowseren så vi let kan udforske og klargøre vores indlæste data. Lær mere om [SQL](http://www.w3schools.com/sql/). Lær mere om geografiske analyser med PostGIS i CartoDB [http://docs.cartodb.com/tips-and-tricks.html#geospatial-analysis](http://docs.cartodb.com/tips-and-tricks.html#geospatial-analysis)

I denne CASE udnytter vi SQL funktioner fra både PostgreSQL og PostGIS til dels  at klargøre vores indlæste data og dels til at [JOINE](http://www.postgresql.org/docs/9.3/static/tutorial-join.html) vores to datasæt.

> **Info**
I videoerne nedenfor vises de samme trin, mens de udføres i CartoDB

##Upload moms-data til CartoDB

1. Opret en konto hos [CartoDB](https://cartodb.com/)
2. Klik "view your tables"
3. Klik "New table" og vælg filen fra [Moms-data](http://virkdata.gitbooks.io/open-data-school/content/data/moms.html)
4. Når filen er uploaded klikkes på kolonne navnene og de omdøbes så vi har en kolonne for: komnavn,branche og omsaetning

<iframe width="100%" height="515" src="//www.youtube.com/embed/glX4yp_ETQQ" frameborder="0" allowfullscreen></iframe>



###Upload kommunegrænser til CartoDB
1. Klik "view your tables"
2. Udpak filen på din computer og vælg alle filerne med navnet KOMMUNE.* og pak dem i en ny .zip fil
3. Klik "New table" i cartodb og vælg filen fra [DAGI-data](http://virkdata.gitbooks.io/open-data-school/content/data/dagi.html)
4. Upload denne nye zip fil med kommunegrænser til CartoDB

<iframe width="100%" height="515" src="//www.youtube.com/embed/aGrRyiSol1I" frameborder="0" allowfullscreen></iframe>



##Klargøring af data

Førend de to datasæt kan forenes (joines) korrekt, er det nødvendigt, at de deler en fælles nøgle. I denne case skal data joines på kommune. DAGI-datasættet med kommunegrænser indeholder en kolonne for kommunekoden og en kolonne for kommunenavnet, mens moms-datasættet alene indeholder en kolonne for kommunenavn. Derfor er vi nødsaget til at bruge kolonnen for kommunenavn til vores data-join. Derudover bruges ikke nøjagtigt samme kommunenavne i de to datasæt. Vi vil derfor forsøge at manipulere data således, at der er to kolonner med samme stavemåde på kommunenavne. Desuden har vi en udfordring med antallet af kommunepolygoner i DAGI-datasættet. Lad os starte med den.



> **Caution**
Det er ikke optimalt at joine på et tekstfelt. En kolonne med kommunekode bestående af heltal vil være meget bedre at anvende til at joine de to datasæt.


####Kommunegrænser

Hvis vi hurtigt inspicerer vores datasæt for kommunerne, kan vi se, at der er langt flere rækkker end der er kommuner i Danmark. Det skyldes måden datasættet er konstrueret på. Geometritypen i datasættet er defineret som en POLYGON og ikke en MULTIPOLYGON. Det bevirker at polygoner, der ikke hører sammen geometrisk har en selvstændig række i datasættet. Til en kommune kan der oftes knyttes flere enkelt polygoner. F.eks. har Amager en selvstændig polygon, som ikke hører til Indre København. Det giver problemer, når vi skal joine til moms-datasættet. Vi kan med SQL lægge kommunepolygonerne sammen for de rækker, der hører til samme kommune.


Hvis vi tæller antallet af rækker, kan vi hurtigt se, at der er flere (311) rækker end de 98 kommuner. Tryk på SQL i højre side, i tabellen i cartodb, og skriv:

```sql
SELECT COUNT(*) FROM kommune
```

> **Comment**
[COUNT](http://www.postgresql.org/docs/9.3/static/functions-aggregate.html) er en AGGREGATE funktion i SQL, der bla. kan bruges til at lave simple statistikker på datasættet. Vi anvender SQL [SELECT](http://www.postgresql.org/docs/9.3/static/sql-select.html) som kun udvælger i data. Vi retter IKKE i datasættet med SELECT.

Vi bruger en særlig [POSTGIS](http://postgis.refractions.net/documentation/) funktion [ST_UNION](http://postgis.net/docs/manual-2.0/ST_Union.html) til at lægge polygoner for samme kommune sammen.

```sql
SELECT st_union(the_geom) as the_geom, komnavn FROM kommune GROUP by komnavn
```
Klik på linket "create table from query" og navngiv den nye tabel kommune_union


> **Comment**
Når man anvender GROUP BY i SQL, lægger man rækker sammen. Derfor skal øvrige kolonner aggregeres. Her lægges geometrierne sammen med en aggregate funktion, som hedder ST_Union() for alle de rækker med samme kommunenavn.

#### Kommunenavne

Da CartoDB er baseret på [SQL](http://da.wikipedia.org/wiki/Structured_Query_Language), vil vi manipulere data med SQL statements. Vi vil oprette en ny kolonne i vores datasæt for kommunegrænser som stemmer overens med dén navngivning af kommunerne, der findes i moms-datasættet.

1. Klik på SQL fanen i tabelvisningen af kommunegrænser
2. Opret ny kolonne med følgende SQL

```sql
ALTER TABLE kommune_union ADD column komnavn_moms character varying
```

Opdatér kolonnen med de eksisterende kommunenavne og endelsen "Kommune". Vi starter med at tilføje denne endelse i vores nye kolonne.


```sql
UPDATE kommune_union SET komnavn_moms = komnavn || ' Kommune'
```

Vi kan nu lave et JOIN for at set om alle vores rækker i vores redigerede kommunetabel kan JOINES på momsdatasættet

Vi bruger LEFT JOIN så vi sikrer os, at vi får alle rækker fra kommunegrænserne og kan se, hvor det ikke har været muligt at joine de to datasæt

```sql
SELECT kom.the_geom,
kom.komnavn_moms,
skat.komnavn,
skat.omsaetning,
skat.branche
FROM kommune_union kom
LEFT JOIN skat_momsomsaetning_2012 skat ON (kom.komnavn_moms=skat.komnavn
  AND skat.branche='Murere')
  ORDER BY kom.komnavn_moms
```

Vi kan se, at der er en række kolonner fra moms-tabellen (komnavn,omsaetning,brancnhe), der har null-værdier. Det skyldes, at databasen ikke har kunne JOINE felterne, og at de altså ikke er ens (endnu). Det første, der springer i øjnene er, at Århus staves med Aa i DAGI-datasættet, mens det staves med Å i moms-datasættet. Lad os rette vores kommune-tabel de steder, hvor stavemåden stadig er forskellig.

```sql
UPDATE kommune_union SET komnavn_moms = 'Århus Kommune' WHERE komnavn_moms = 'Aarhus Kommune';
UPDATE kommune_union SET komnavn_moms = 'Brønderslev-Dronninglund Kommune' WHERE komnavn_moms = 'Brønderslev Kommune';
UPDATE kommune_union SET komnavn_moms = 'Høje-Taastrup Kommune' WHERE komnavn_moms = 'Høje Taastrup Kommune';
UPDATE kommune_union SET komnavn_moms = 'Københavns Kommune' WHERE komnavn_moms = 'København Kommune';
```

Når vi afvikler ovenstående JOIN kan vi se, at der stadig er rækker, der ikke JOINEs fordi ordet "Kommune" i nogle rækker er stavet med lille "k". Vi opdaterer moms-datasætte og sikrer, at Kommune er stavet med stort "K"

```sql
UPDATE skat_momsomsaetning_2012 set komnavn = replace(komnavn, 'kommune', 'Kommune')
```

Der er stadig udfordringer. Christiansø er ikke en kommunal administrativ inddeling og findes ikke i moms-datasættet. Vi vælger at slette denne i vores kommunegrænser.


```sql
DELETE FROM kommune_union WHERE komnavn_moms = 'Christiansø Kommune'
```

Endelig kan vi se, at den eneste kommune, der ikke kan joines, er Norddjurs Kommune. Den findes ikke i moms-datasættet. Det kan muligvis skyldes, at der er mindre end fem virksomheder af den type i kommunen. Disse medtages ikke kan man se på [Virk Data](http://datahub.virk.dk/dataset/momsomsaetning-gennemsnit)

<iframe width="100%" height="515" src="//www.youtube.com/embed/kFr_rBbzWF0" frameborder="0" allowfullscreen></iframe>


Vi er nu klar til at producere selve kortet
