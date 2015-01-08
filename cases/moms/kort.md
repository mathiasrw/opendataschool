##Kort med CartoDB

I Cartodb skelnes der mellem "tables" og "visualisations"

###En tabel repræsenterer de rå data i Cartodb-databasen

Vi har nu forberedt to tabeller:

1. En tabel med kommuneafgænsninger med kommunenavne savernde til de navne, der findes i moms-datasættet. Vi har derudover lagt polygoner sammen således, at der kun findes én mulitipolygon pr. kommune

2. En tabel med den gennemsnitlige momsbetaling fordelt på kommune og branche

###En visualisation repræsenterer en særlig visualisering af en eller flere tabeller

I dette tilfælde vil vi joine de to tabeller on-the fly for at vise den gennesnitlige momsbetaling i murerbranchen fordelt på kommuner


### Visualisation

For at vi kke bruger for meget plads på vores CartoDB konto vil vil ikke lave nye tabeller, men lave et data join on-the-fly med SQL

Bemærk, at vi henter kolonnerne cartodb_id og the_geom_webmercator


```sql
SELECT kom.cartodb_id,kom.the_geom_webmercator,
kom.komnavn_moms,
skat.komnavn,
skat.omsaetning,
skat.branche
FROM kommune_union kom
LEFT JOIN skat_momsomsaetning_2012 skat ON (kom.komnavn_moms=skat.komnavn
  AND skat.branche='Murere')
  ORDER BY kom.komnavn_moms
```

> **Info**
cartodb_id skal bruges til interaktioner i kortet som klik og hover
the_geom_webmercator er en kolonne, der ikke vises, men bruges "under-the-hood" til rendering af kortet




<iframe width="560" height="315" src="//www.youtube.com/embed/dsIbZ48niJg" frameborder="0" allowfullscreen></iframe>
