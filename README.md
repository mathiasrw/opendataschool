Virkdata Open Data School
=======

Denne bog har til formål at give en introduktion til brugen af åbne data ved hjælp af en række mindre
øvelser.

Åbne data kommer i mange former og størrelser, og vi vil derfor med denne bog gerne give en introduktion
til hvordan man kan bruge nogle af de mange typer af åbne data. Bogen er på ingen måde færdig, den
bliver løbende udvidet med øvelser på nye datasæt og nye typer af samstillinger.

Bogen er opbygget som en række af øvelser, som gradvist bliver sværere samtidig med, at den inddrager
forskellige datakilder. Endemålet er at du som læser har fået en grundlæggende indsigt i, hvordan
offentlige åbne data kan omsættes til indsigt og viden. Vi håber du vil kunne bruge dette afsæt  til at finde
på egne ideer og måder at omsætte åbne data til værdi.
Denne bog er udviklet som open source, og vi vil opfordre alle til at bruge den i lige den kontekst der giver
mening for dem. Ligeledes vil vi også opfordre til at komme med tilføjelser, enten direkte som pull
requests(se nedenfor) eller ved at kontakte os.


Bidrag selv til bogen
=======

Fork [bogens repository](https://github.com/virkdata/opendataschool) på Github og lav Pull requests


Bogen er skrevet med [gitbbok](https://www.npmjs.com/package/gitbook) og kræver [NodeJS](http://nodejs.org/) er installeret.


Når NodeJS er installeret kan gitbook og afhængigheder installeres  på følgende måde:

Installér gitbook
```sh
npm install gitbook -g
```

Installér andre krævede nodeJS moduler


```sh
npm install
```

Man kan rette i bogen lokalt på eget EDB anlæg med en live reload server, der automatisk opdaterer browseren når filerne ændres:

```sh
gitbook serve
```

Derefter kan man se bogen på:

http://localhost:4000/

Bogen kan bygges og publiceres med [Grunt](http://gruntjs.com/)
======

````
$ grunt publish
```

Derefter kan bogen ses på:

http://virkdata.github.io/opendataschool
