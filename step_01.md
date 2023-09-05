
# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```


## Forudsætning.

* NodeJS installeret.
* MongoDB Community Server (Local Installation).
* PostMan (https://www.postman.com/) eller Insomnia (https://insomnia.rest/).

## Documentation
**Basis (*der findes mange entry level sites derude*)** 
* https://www.w3schools.com/nodejs/

#### Til brug i dette materiale

**NodeJS**
* https://nodejs.org/en/docs
* https://expressjs.com/

** MongoDB **
* https://www.mongodb.com/docs/
* https://mongoosejs.com/docs/



## Installation
* [NodeJS (LTS - Version).](https://nodejs.org/en) 
* [MongoDB/Compass](https://www.mongodb.com/docs/manual/administration/install-community/)

```
NB: MongoDB: det anbefales at i genstarter jeres maskine efter installation uanset hvad programmet fortæller jer. Compass er jeres GUI til mongoDB - det er det program hvor vi kan se vores data, som et 'excel agtigt' program.
```

Check om i har MongoDB korrekt installeret, brug følgende kommando i terminalen.:
```
mongod --version
```

Check om i har NodeJS installeret, brug følgende kommando i terminalen.:
```
node -v
```

# 1. Step 1 : Kom igang med Node, helt basic.

* Opret en mappe og kald den "node-server"
* Åben mappen i VisualCode.
* Opret en index.js fil i mappen.

indsæt i index.js filen:
```
console.log('Hello Node');
```

Åbn for din ``terminal`` og skriv:
```
node index
```

I terminalen skulle der gerne logges:
```
Hello Node
```

Så node fungerer på den måde, at du benytter kommandoen ``node`` og den fil du vil "starte" som regle ``index.js`` altså: ```node index``.

# 2. Vi gå til makronerne og laver en webserver.

Der er rigtig meget at vende i ``nodeJS``. Det er jo en fuld backend maskine så vi kan i virkeligheden lave alt hvad vi som personer kan lave med vores computer. Derfor bliver vi nødt til at fokusere på det vi skal bruge node til. Nemlig at køre en server og levere data via endpoints, derigennem vil vi lære ``NodeJS`` at kende ganske langsom og som behovene opstår vil nysgerrigheden ikke kunne holde os tilbage.

Vi starter også med at udvikle NodeJS i "commonjs" - en udgave af javascript som er en slags forgænger for den modularitet vi oplever i javascript sproget i dag. Vi benytter ``import`` og ``export`` i ES6 javascript. I CommonJS benytter man ``require``og ``exports.module``.

## 2.1 Projekt Struktur

Vi starter med vores ``index.js`` hvor vi opretter et entrypoint for hele vores applikation.

index.js
```JavaScript
const server = require('./lib/server');
const app = {};

app.init = () => {

    console.log('app.init()')
    server.run();

};

app.init();
```

* Opret en mappe i dit projekt du kalder ``lib``
* i den mappe opretter du en ``server.js`` fil.

I ``server.js`` indsætter du:
```JavaScript
const server = {};

server.run = () => {

    console.log('Run Server')

};

module.exports = server;
```

Nu kan du køre commandoen ``node index`` i terminalen og se at der logges i terminalen:

```
app.init()
Run Server
```

Du kan også se at vi i ``index.js`` "require" vores ``server.js`` fil i toppen.
``const server = require('./lib/server');``

Og du kan se i ``server.js`` at vi eksportere filen i bunden ``module.exports = server;``
Så som vi kender det fra modules i JS så kan vi *code-splitte* i lige så mange filer vi vil.

Nu er meningen vi skal have en server op at køre og dertil er der et indbyggey ``http`` modul. Men nogle har gjort det endnu nemmere og lavet en meget populær pakke der heder ``node-express``.

Så vi har brug for node modules i vores projekt.

Åbn, terminalen og skriv:
``
npm init -y
``

Nu vil vi få oprettet en ``package.json`` til versionstyring af vores pakker/moduler vi henter fra node_modules.

For at installere express-serveren skal vi igen skrive en kommando.

Åbn, terminalen og skriv:
```
npm install express
```

Dette vil installere express som pakke og du vil kunne se den optræde i package.json filen, under dependencies (afhængigheder).

```JSON
{
  "name": "node-server",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "directories": {
    "lib": "lib"
  },
  "scripts": {
    "run-server": "node --watch index"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

Så nu hvor vi har fat i package.json filen. Så læg mærke til "scripts" objektet. Her har jeg oprettet et lille script. Et script der starter vores node applikation og "watcher". Det betyder at den ligesom live-server opdatere når den ser fil ændringer.

Samtidigt vil "run-server" optræde i din NPM-SCRIPTS i nederste venstre hjørne i ``VisualCode``. Og det vil senere være en fordel at starte serveren via den knap.

Og alternativt kan du starte ved at skrive:
```
npm run run-server
```
Eller du kan bare skrive det hele:
```
node --watch index
```

Luk package.json filen, alle er glade.

## Opret serveren

Nu har vi installeret ``express`` som er et bibliotek der gør det nemmere for os at oprette en webserver til vores formål.

Åbn ``server.js`` og start med at lave en reference til vores express bibliotek.

```JavaScript
const express = require('express');
```

Og derefter opretter vi en constant til selve ``express`` biblioteket.

```JavaScript
const express = require('express');

// Express Server
const expresServer = express();
```

I vores ``server.run`` funktion opretter vi en express event listener som lytter på alle de ``requests`` (forespørgelser) der måtte komme mod vores server.

Nu er det vores ansvar at tage imod ``requests`` og sende et ``response`` tilbage.

I ``run()`` funktionen indsætter vi:
```JavaScript
// Opretter en variable der holder på vores port nummer.
const port = 3000;

// Express Event der lytter på alle 'requests`
expresServer.listen(port, () => {

    console.log(`Serveren lytter på port http://localhost:${port}`)

});
```

Så ``run()`` funktionen ser således ud:
```JavaScript
server.run = () => {

    // Opretter en variable der holder på vores port nummer.
    const port = 3000;
    console.log('Run Server')

    // Express Event der lytter på alle 'requests`
    expresServer.listen(port, () => {

        console.log(`Serveren lytter på port http://localhost:${port}`)

    });

};
```
Hvis vi køre ``node --watch index`` nu vil vores server køre. Men vi har ingen ting at "kalde".
Vi har ingen "ruter" vi har bare startet en server der ikke indholder noget.

Enhver server har brug for mindst ét endpoint vi kan kalde. En server er i virkeligehden bare et ip-nummer. En server kunne være (tilfældigt opfundet) ``212.194.210.34`` og så kan du have dit domæne ``fedside.dk``til at pege på denne IP adresse.

Så hvis sender et ``request`` imod ``http://212.194.210.34`` så vil man, hvis det er en hjemmeside forvente at få `html`. Hvis det er et system vil man måske forvente noget data. Men normal vil man se en ``rute`` eller ``route``. Altså ``http://212.194.210.34/en-rute`` eg. ``http://212.194.210.34/users``

Så hvis vi skal kunne tage imod disse ``requests`` så skal vi oprette disse ``ruter/routes``.

# En forside til vores server.

Vi starter med at lave en forside til serveren. En forside til det man tit kalder ``back-office``.
Det hedder sådan fordi hensigten er, at det er igennem denne frontend, du ændre data. Dvs. et CMS - Content Management System. -> Indholdsstyrings System for den backend vi laver. 

Vi splitter det lidt mere op for ikke at forvirrer os selv. Men det er oplagt, at man giver nulighed for at logge ind på serveren og herigennem få en status og eventuelt kunne oprette, update og delete.

## Client.

Så vi opretter vores indgang til ``back-office`` ved at lave en mappe vi kalder ``client``.

Vi navngiver den ``client`` for at gøre det tydeligt at alt i denne mappe er henvendt mod "client-siden".
Dvs. det er her vi laver frontend til serveren og kun her. 

I klient mappen opretter vi følgende:

* index.html - en basic hello world template.
* en ``scripts`` mappe.
* en ``styles`` mappe.
* en ``client.js`` i ``scripts`` mappen. -> indsæt en console.log('client');
* en ``client.css`` i ``styles`` mappen. -> Lav en body background-color.

Client Folder Struktur.

* Client
  * index.html
  * scripts
    * index.js
  * styles
    * index.css


I ``index.html`` linker du til dine client filer.

```html 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <script src="./scripts/client.js" defer></script>
    <link rel="stylesheet" href="./styles/client.css">

    <title>Client</title>
</head>
<body>
    <h1>Client Server</h1>
</body>
</html>
```

Nu har vi ingredienserne til en basic vanilla frontend der kan fungere som backoffice og i det mindste levere en forside til dem der kalder vores side/ip via en browser eller lign. (jvf. ``http://212.194.210.34/``)

Så nu mangler vi bare at aflevere denne side når der kommer et ``request`` imod ``http://localhost:3000/``.

Det gør vi ved at oprette et ``endpoint`` til lige netop den rute.``http://localhost:3000``

I ``server.js`` indsætter vi.
```JavaScript 
expresServer.get('/', (req, res) => {

})
```
Læg mærke til at vi laver en rute imod `/` med metoden `get` => ``get('/',`` Det betyder at vi opretter en rute der forventer et ``request`` imod "forsiden" eller "roden" af vores site/ip. 

Altså ``http://localhost:3000`` eller ``http://localhost:3000/``

Og nu skal vi så sende et ``response`` tilbage, der mathcer det ``request`` vi har modtaget.

Det gør vi ved at sende en ``header`` der forklare hvilket indhold modtageren skal forvente og noget data der matcher den header vi sender med. 

Så hvis vi f.eks sender en ``header`` der fortæller at vi sender ``html``, så **skal** vi sende ``html``, enten som fil eller tekst. Hvis vi sender ``json`` så skal vi lave en ``json``header.

Til vores ``client`` side sender vi ``html``, vores index.html og vi sender det som filen vi har oprettet. 

I vores ``get`` endpoint til ruten ``/`` indsætter vi:

```JavaScript
res.setHeader('Content-Type', 'text/html; charset=utf-8');
res.sendFile(path.resolve(__dirname, '../client/index.html'));
```

Så metoden ser således ud:

```JavaScript
expresServer.get('/', (req, res) => {

    res.setHeader('Content-Type', 'text/html; charset=utf-8');
    res.sendFile(path.resolve(__dirname, '../client/index.html'));

});
```
Her kan vi se at vi fortæller i vores ``header`` at vores ``content-type`` er ``text/html; charset=utf-8``.

Og i næste linje fortælle vi at vi vil sende en fil
``res.sendFile(path.resolve(__dirname, '../client/index.html'));``

Begge metoder sidder på ``res`` = ``response``, altså det svar vi gerne vil sende tilbage til vores forspørger (``request``).

Så ``res.setHeader`` er vores ``response header``.
Og ``res.sendFile()`` er den content der matcher vores headers ``content-type`` = ``html``.

**path** og **__dirname**.

I ``res.sendFile(path.resolve(__dirname, '../client/index.html'));`` ser vi to ``NodeJS`` specifikke metoder. ``__dirname`` er *indbygget* og ``path`` skal ``requires`` men ikke npm installeres.

Så i toppen af ``server.js`` skal vi ``require`` ``path`` indsæt i ``server.js``:
```JavaScript
const path = require('path');
```

Hemmeligheden bag ``path.resolve(__dirname, '../client/index.html')`` er at vi ønsker at lige meget hvor på computeren vi placerer vores projekt, så vil `../client/index.html` **ALTID** være relativt placeret i forhold til vores projektrods placering eg **__dirname**. Så ``path`` ``resolver`` løser dette for os ved at få den absolutte sti og relativere den.

Så nu kan alle der henter projektet smide det hvor de vil.

Så inden vi starter vores server så skal vi også fortælle serveren at vi har nogle filer der gerne må blive delt med omverden. Det handler om vores `client` filer js, css osv.

Det gør vi ved at give adgang til "statisk materiale" altså filer der ligger på serveren.

indsæt i ``server.js``, lige efter der hvor vi satte ``expresServer`` variablen op.
```
const expresServer = express();
expresServer.use(express.static(path.resolve(__dirname, '../client')));
```
Nu kan vi teste vores server. Start den, med ``--watch`` command.

Åbn en browser og indsæt http://localhost:3000

Du skulle meget gerne kunne se en forside med en header der siger: Client Server. Tjek også at der er en ``hello client`` i browser consollen. Og at din "background-color" på body også er tilstede.

Så har du *hul igennem*.

Nu kan du ændre lidt i Client koden og refreshe din browser. Tilføj et afsnit til index.html. 

```HTML
<p>Backoffice til http://localhost:3000</p>
```

**FEDT!?!** Vi har lavet en webserver der lige som liver server kan levere en hjemmeside.
