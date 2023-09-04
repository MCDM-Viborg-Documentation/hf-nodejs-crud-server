# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* Step 2

## Step 3 - Users Create

Vi har opretter vores ``read`` endpoint. Vi læser godt nok vores fil data men vi lader det være sådan for nu.

Lad os starte med at oprette brugerer, vi derefter kan læse fra databasen.

Men lad os starte med at lave et `endpoint` der kan tage imod et `post request` med et user objekt.

Vi åbner ``server.js`` og opretter en `post endpoint` til `/users`.

```
// Server Endpoints (Create)
expresServer.post('/users', (req, res) => {

    console.log(req.body)
    res.send({"succes" : "true"})
    
});
```

Åbn nu ``postman`` og opret et ``post request`` med et body objekt:
```
{
    "firstname" : "Hans Kristian",
    "surname" : "Andersen",
    "age": 132,
    "street" : "Dæhovoer Vej",
    "zip" : 5201 
}
```
![alt text](https://mcdm-resources.ams3.cdn.digitaloceanspaces.com/tutorials/crud/crud_postman_01.png)

Nu kan vi se at vores ``payload`` altså ``req.body`` objekt kommer igennem. Det betyder at vi nu vil kunne samle en ``html formular`` op og ``poste`` til vores ``endpoint``, men vi fortsætter med at teste alt funktionalitet via post man.

``req.body`` er vores ``request`` ``body``. Så når vi i vores client kode tilgår sådan et endpoint så benytter vi ``fetch``med en post metode og sender vores ``object`` med som en body på ``fetch``. Feks.

```JavaScript
   fetch('http://localhost:3000/users', {
            method : 'POST',
            headers : {
                'Content-Type' : 'application/json'
            },
            body: JSON.stringify({
                "firstname" : "Hans Kristian",
                "surname" : "Andersen",
                "age": 132,
                "street" : "Dæhovoer Vej",
                "zip" : 5201 
            })
      
   })
```

og det er det ``posr request`` vi tager imod på vores ``users endpoint`` ved hjælp af ``req.body``.

Men vi skal håndtere denne opgave i vores ``users.js`` fil.

Så Åbn ``lib/users.js`` og opret følgende create metode:

```JavaScript
users.create = (payload, callback) => {

    console.log('users.create payload:', payload);

    callback(200, {'success': 'true'});

};
```

Kald nu denne metode istedet, fra ```server.js``` filen.

```JavaScript
// Server Endpoints (Create)
expresServer.post('/users', (req, res) => {

    users.create(req.body, (code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });
    
});
```

Resultatet skulle meget gerne være det samme som før men nu har vi flyttet funktionaliteten over i ``users.js`` filen. Vi benytter stadigvæk ``callback`` men vi sender ``req.body`` med som extra parameter. 

Derfor modtager vores ``users.create`` modsat ``users.read`` nu 2 parametre. ``payload`` og ``callback``. 

``callback`` er det samme som før, den funktion vi skal kalde når arbejdet er overstået. Og ``payload`` er det data vi har fået fra ``request´et`` via ``body``.

Så nu har vi *hul igennem* og kan sende data retur. Så nu mangler vi *bare* at gemme vores body data i databasen, sende en success kode og den oprettede bruger retur.

## MongoDB og Mongoose.

**Dokumentation for arbejde med MongoDB Compass GUI er ikke tilgængeligt endnu og vil være et selvstændig dokument, vi arbejder med dette i undervisningen, dette dokument antager at MongoDB køre og at man kan åbne MongoDBCompass og logger på localhost. - den lokale version*

**Mongoose**

Mongoose er et hjælpe bibliotek der både gør det let men også giver mulighed for meget rigid styring af den data vi gemmer. Vi tager det lidt *loose* til at starte med, men jeg vil anbefale at i læser op på alle de muligheder der er for at styre validiteten på vores modeller. 

Læs disse to dokumenter. - Lidt før og lidt efter :).

Det hjælper at lære lidt om det, og så vende tilbage til dokumentationen så man bedre kan forstå eksemplerne.

Men start lige med læse starten af disse to dokumenter.

Scheme:
* https://mongoosejs.com/docs/guide.html

Models:
* https://mongoosejs.com/docs/models.html

## Installering af Mongoose.

Moongoose er en ``npm`` pakke og kan installeres via kommandoen:

```code
npm install mongoose
```

## Database Script

Efter mongoose er installeret har vi brug for et script der får kontact til MongoDB og den database vi ønsker at benytte.

Connection String: Den stil der er til vores MongoDB installation.

```
mongodb://localhost:27017
```

Det er en lokal MongoDB som kun eksistere på vores computer, god til at teste på.

Opret en ``middleware`` mappe i `lib` mappen. Opret derefter en `database.js` fil i ``middleware`mappen.

* lib
    * middleware
        * database.js

Indsæt nedestående script i ``database.js`` filen.

```JavaScript
const mongoose = require('mongoose');
mongoose.set('strictQuery', false);

const database = {};
database.name = 'NY-DB';

database.connect = () => {

    mongoose.connect('mongodb://127.0.0.1:27017/' + database.name)
    .then( () => {

        console.log('Database kontakt etableret.')

    })
    .catch((error) => {

        console.log('Database kontakt fejlede');
        console.log(error);
        process.exit(1);

    })
}

module.exports = database;
```

Læg mærke til, at vi ``require('mongoose')`` derefter opsætter vi et ``database`` module.

Vi har en ``connect`` metode og den metode benytter mongoose til at forsøge at åbne en kontakt til vores database. Vores database navn i MongoDB er defineret i ``database.name = 'NY-DB'``.

Åbn nu ``server.js`` og ``const database = require('./middleware/database')``.

Og så indsæt lige før vores første "rute";

```JavaScript
// Skab kontakt til database. Vi benytter "connect" metoden i vores database fil og skaber kontakt til vores database.
database.connect();
```

Hvis du genstarter serveren skulle vi gerne se en log i terminalen:
```
Database kontakt etableret.
```

Og hvis vi åbner MongoDB Compass og/eller refresher Compass DBs så vil vi se en NY DB med det navn vi har tildelt den.

## Afslutning Step 03.

Prøv at slette den database du har oprettet i Compass - genstart din server og opret en ny.

Scheme:
* https://mongoosejs.com/docs/guide.html

Models:
* https://mongoosejs.com/docs/models.html

Nu skal vi til at arbejde med Schema og Model, men vi finder det i Step 4.
