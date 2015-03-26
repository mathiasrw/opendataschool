## Kortet

Vi har nu to tabeller i CartoDB **cvr_count** og **folketal**

Vi skal nu joine de to tabeller på kolonnen med kommunekode. Det kan gøres med SQL, men vi vil gøre det med CartoDBs **merge** funktion.

Inden vi kan **merge** skal vi lige fjerne overfødige data fra tabellen **folketal**

> **Info**
Alle trin gennemgås også i videoen nedenfor


I datasættet er der rækker med som ikke repræsenterer kommuner. Det er folketal fra regioner og gor hele landet. Det alle de rækker, hvor kolnnen omrade er mindre end 100.


Klik på tabellen **folketal** og åben SQL fanen i højre side.

```sql
SELECT * FROM folketal WHERE omrade < 100
```

Slet disse rækker med:

```sql
DELETE FROM folketal WHERE omrade < 100
```

Derudover skal vi foranstille et **0** så vi kan joine med tabellen **kommune_merge_kode** med kommunegrænser
som vi oprettede i en tidligere øvelse.

Opret en nye kolonne:

```sql
ALTER TABLE folketal ADD column kom_kode_ny character varying;
```

Indsæt kommunekoden + et foranstillet "0":

```sql
UPDATE folketal SET kom_kode_ny = '0' || omrade
```

Klik på kolonnen **indhold** og omdøb den til **personer**

Vælg tabellen **cvr_count** og omdøb på samme måde kolonnen **antal** til **virksomheder**

Klik på menupunktet Edit>Merge with table

Vælg **column join**

I venstre panel vælges kolonnen **beliggenhedsadresse_kommune_kode**. Det er den kolonne vi vil joine med fra ** cvr_count** tabellen.

I højre side vælges tabellen **folketal** og vælg kolonnen **omrade**. Dermed joiner vi de to tabeller på kommunekoden.

Navngiv tabellen **cvr_count_folketal_merge**

Åben nu tabellen **cvr_count_folketal_merge** og vælg igen Edit>Merge with table.

Vælg at joine med tabellen **kommune_merge_kode** på kolonnerne **kom_kode_ny** og**komkode**

Navngiv den nye tabel eksempelvis **virk_pr_person**

Nu opretter vi en ny kolonne, der viser hvor mange virksomheder, der findes pr. person fordelt på kommune.


Opret kolonnen

```sql
ALTER TABLE virk_pr_person ADD column virk_person double precision;
```

Opret beregnet kolonne med ny værdi.

```sql
UPDATE virk_pr_person SET virk_person = virksomheder/personer::double precision
```


Nu har vi alle data så vi kan lave kortet i CartoDB.

Klik på fanen **Wizards** og vælg **choropleth**. Vælg kolonnen **virk_person** vi netop har oprettet.

Vælg også **Legends** og vælg en titel f.eks. **Virksomheder pr. person**

<iframe width="100%" height="515" src="//www.youtube.com/embed/OtqYZiaKWqE" frameborder="0" allowfullscreen></iframe>
