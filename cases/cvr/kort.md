##Kort med CartoDB

For at visualisere resultatet i CartoDB skal vi lave et **merge** mellem to tabeller. Vi skal anvende kolonnerne med geometri fra kommunetabellen som skal **merges** med tabellen vi netop har uploaded. Det kan også udføres med et SQL JOIN, men denne gang prøver vi **merge** funktionen i CartoDB.


For at konstruere kortet skal følgende trin gennemføres.


> **Info**
Alle trin gennemgås også i videoen nedenfor


1.Opret en kommunegrænse tabel med de nødvendige kolonner:

Vi har behov for en kolonne med kommunekoden fra kommunetabellen. I "Momskortet" JOINEDE vi geografi med momsdata på kommunenavnet. Denne gang har vi en kommunekode i top_branche tabellen og derfor er det hele lidt lettere.

Vi kan lave en ny kommunetabel med følgende sql (Se video nedenfor)

```sql
SELECT st_union(the_geom) as the_geom, komnavn,komkode  FROM kommune GROUP by komnavn,komkode
```
Klik "Create table from query"

2.Merge tabellerne til ny tabel:

Vælg tabellen "top_brancher". Klik i menupunkt Edit->Merge with table
Vælg den kolonne, der skal JOINES på (se video)


3.Visualisér tabellen:

Visalisér tabellen på samme måde som i [Momskortet](http://virkdata.gitbooks.io/open-data-school/content/cases/moms/kort.html).


Se video af workflowet her:

<iframe width="100%" height="515" src="//www.youtube.com/embed/rLywGiUNIkM" frameborder="0" allowfullscreen></iframe>
