Bidrag selv til bogen
=======

Fork selv [bogens repository](https://github.com/virkdata/opendataschool) på Github og lav Pull requests. Virkdata vil merge relevante pull requests som automatisk vil indgå i denne bog.


Bogen er skrevet med [gitbook](https://www.npmjs.com/package/gitbook) og kræver [NodeJS](http://nodejs.org/) er installeret.


Når NodeJS er installeret kan gitbook og afhængigheder installeres  på følgende måde:

Installér gitbook
```sh
npm install gitbook -g
```

Installér andre krævede nodeJS moduler


```sh
npm install
```

Man kan rette i bogen lokalt på eget EDB anlæg med en live reload server, der automatisk opdaterer browseren, når filerne ændres:

```sh
gitbook serve
```

Derefter kan man se bogen på:
http://localhost:4000/


Når de ønskede ændringer er udarbejdet sendes et Pull request til

https://github.com/virkdata/opendataschool
