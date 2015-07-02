# Data til virksomheder pr. indbygger

For at svare på spørgsmålet om antallet af virksomheder pr. indbygger fordelt på kommune, har vi behov for at lave en sammentælling af virksomheder pr kommune.Da vi allerede har indlæst CVR data i vores lokale PostgreSQL database, kan vi let lave optællingen med følgende SQL:

> **Info**
Alle trin gennemgås også i videoen nedenfor

Antal virksomheder fordelt på kommune
===
```sql
SELECT count(*), beliggenhedsadresse_kommune_kode
 FROM cvr.virksomheder
  GROUP BY beliggenhedsadresse_kommune_kode
   ORDER BY beliggenhedsadresse_kommune_kode
```


Vi får følgende (trunkerede resultat):

|count | beliggenhedsadresse_kommune_kode|
|-------|----------------------------------|
|70947 | 101|
|11968 | 147|
| 3594 | 151|
| 2593 | 153|
|....|...|


Dette resultat skal nu eksporteres til CartoDB som en ny tabel. Vi gør det på tilsvarende måde som i "Momskortet":

Marker SQL i PgAdmin og vælg i meuen: Query-> Execute to file. Kald filen cvr_count.csv.

Log ind på CartoDB og opret ny tabel som i [CASE om momsdata](/../moms/data.html) og upload cvr_count.csv

> **Comment**
Hvis man har et unix-baseret (eller windows med WGET eller CURL) system kan man uploade i et skridt med SQL med dette hack:
```sql
COPY
(SELECT beliggenhedsadresse_kommune_kode, count(*) as antal
 FROM cvr.virksomheder
  WHERE beliggenhedsadresse_kommune_kode <> ''
   GROUP BY beliggenhedsadresse_kommune_kode
   ORDER BY antal DESC)
 TO PROGRAM 'cat <&0 > /tmp/data.csv; curl -F "file=@/tmp/data.csv;filename=cvr_count.csv" "https://{CARTODB_USER}.cartodb.com/api/v1/imports/?api_key={APIKEY}"'
   DELIMITER ','
   CSV HEADER;
```


Folketal fra Danmarks Statistik
===

[Danmarks Statistik](http://www.statistikbanken.dk/) har folketællinger fordelt på kommune. Man kan selv vælge sine kolonner og hvilket format man vil have data i. I denne øvelse anvender vi ikke denne løsning, men i stedet henter vi direkte ind i CartoDB med et API, hvor man kan konstruere sin egen forespørgsel mod Danmarks Statistik og dermed hente data direkte ind i CartoDB. Den forespørgsel vi skal bruge for at hente folketal med API'er ser således ud.


http://api.statbank.dk/v1/data/FOLK1/CSV?valuePresentation=Code&OMRÅDE=*&TID=2015K1

Log ind på CartoDB og opret en nye tabel og indsæt adressen ovenfor. Omdøb tabellen til **folketal** Dermed oprettes tabellen automatisk og det er ikke nødvendigt at uploade en fil.

>**Comment**
Du kan læse mere om [Danmarks Statisks API her](http://www.dst.dk/da/Statistik/statistikbanken/api), samt lave andre forepørgsler.

Nu går vi videre til at danne selve kortet og resten af øvelsen foregår i CartoDB.

<iframe width="100%" height="515" src="//www.youtube.com/embed/Csbh8LEUtp8" frameborder="0" allowfullscreen></iframe>
