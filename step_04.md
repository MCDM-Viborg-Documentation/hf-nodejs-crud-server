# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* step 03

## Step 4 : Mongoose, Modeller & Scheme.

Vi skal installere en pakke:
```
npm install cors
```

Herefter skal vi indsætte to `use` funktionaliteter i `server.js`

```JavaScript 
expresServer.use(express.json());
expresServer.use(cors());
```

HUSK: ``const cors = require('cors');``, vi skal require vores cors bibliotek som vi lige har installeret.


Man kan arbejde med data i MongoDB på mange måder. Vi benytter en bibliotek der hjælper os med at gøre det nemmere at oprette modelle som vi kan gemme i databasen.

En model og scheme er en beskrivelse af det data vi gerne vil gemme.

I vores tilfælde vil vi gerne gemme en bruger.

```
"firstname" : "Hans Kristian",
"surname" : "Andersen",
"age": 132,
"street" : "Dæhovoer Vej",
"zip" : 5201 
```

Så i vores tilfælde skal vi have lavet en Model og Scheme der understøtte dette objekt.

Vi starte med at lave en mappe til vores modeller.

Opret `models` mappe i `lib` mappen. I `models`mappen opretter vi en fil vi navngiver `user.model.js`

* lib
    * models
        * user.model.js

I vores `user.model.js`fil skal vi have oprettet en UserSheme og User Model.

Indsæt følgende i `user.model.js`.
```
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    firstname: {type: String},
    surname: {type: String},
    age: {type: Number},
    street: {type:String},
    zip : {type:Number},
})

module.exports = mongoose.model('user', userSchema);
```
Læg mærke til, vi `require('mongoose')`. Herefter opretter vi `const userScheme` og definere det som et nyt `new mongoose.Schema()`.

Som vi kan se beskrive skemaet vores bruger objekt. Vi definere vores properties, firstname, surname osv. Og vi tildeler dem en type, String, Number, Array, Object, alt efter hvad vi forventer de enkelte elementer skal indeholde.

Tilsidst eksportere vi det som en `model` en user model.
``mongoose.model('user', userSchema);``

Nu er vores model på plads. Nu kan vi bruge denne model til at gemme vores objekter i databasen.

Så lad os åbne `users.js` og i vores `users.create` metode skal vi oprette vores bruger i databasen.

Men vi starter med at require vores user model i toppen af `users.js`
`const User = require('./models/user.model');`

Nu omdanner vi vores `users.create` funktion:

```JavaScript
users.create = (payload, callback) => {

    console.log('users.create payload:', payload);

    User.create(payload).then( () => {
  
        callback(200, {'message' : 'Ny bruger er oprettet'});

    })

};
```

Læg mærke til, at vi benytter vores model `User` og køre en metode `create` og i `create` metoden sender vi vores payload med, altså vores nye bruger.

Thats it! 

Nu skulle du gerne gemme en bruger hver gang du post via postman.

## Afslutning Step 04.

Nu har vi for alvor hul igennem.

I step 5 skal vi lære lidt om hvordan vi læser data og f.eks finder udaf om brugeren vi opretter allerede er i systemet.

Men inden da, skal vi have udvidet vores bruger med en email-adresse.

Tilføj en Email adresse til vores bruger.