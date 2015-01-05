Virkdata Open Data School
=======

Denne Bog er skrevet af Virkdata med henblik på....


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
