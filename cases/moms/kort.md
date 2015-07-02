#Kort med CartoDB

I Cartodb skelnes der mellem "tables" og "visualizations"

###En tabel repræsenterer de rå data i Cartodb-databasen

Vi har nu forberedt to tabeller:

1. En tabel med kommuneafgrænsninger med kommunenavne svarende til de navne, der findes i momsdatasættet. Vi har derudover lagt polygoner sammen, således at der kun findes én mulitipolygon pr. kommune

2. En tabel med den gennemsnitlige momsbetaling fordelt på kommune og branche

###En visualisation repræsenterer en særlig visualisering af en eller flere tabeller

I dette tilfælde vil vi joine de to tabeller on-the fly for at vise den gennemsnitlige momsbetaling i murerbranchen fordelt på kommuner.


### Visualisation

For at vi kke bruger for meget plads på vores CartoDB konto vil vi ikke lave nye tabeller, men lave et data join on-the-fly med SQL. Alternativt kunne vi oprette en ny tabel med de joinede tabeller.



1. Klik under "tables" og vælg skat_momsbetaling_2012
2. Paste denne SQL ind i SQL fanen:

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
3. Øvest i midten skiftes fra "DATA VIEW" til "MAP VIEW"
3. I højre side vælges fanen "wizards". Her vælges choropleth og bruges column=omsaetning
4. Vælg CSS fanen og tilføj en sort farve for null-værdierne (Læsø m.f) :

```css
#skat_momsomsaetning_2012 [ omsaetning = null] {
  polygon-fill: #000000;
}
```

5. Vælg "legend" og tilføj titel
6. Vælg infovindue og konfigurér felter, der skal vises ved klik og hover (når man holder musen over et punkt/område)
7. Nederst i venstre hjørne vælges basemap og under "options"
9. Klik visualize og giv et navn
10. Klik "Share" og del kort som url, iframe eller javascript



> **Info**
Bemærk, at vi henter kolonnerne cartodb_id og the_geom_webmercator. cartodb_id skal bruges til interaktioner i kortet som klik og hover
the_geom_webmercator er en kolonne, der ikke vises, men bruges "under-the-hood" til rendering af kortet

Se hele gennemgangen her:


<iframe width="100%" height="515" src="//www.youtube.com/embed/dsIbZ48niJg" frameborder="0" allowfullscreen></iframe>


### Arbejd selv videre

Prøv selv at lave kort med andre brancher, ved at ændre på branchen i SQL-fanen.
