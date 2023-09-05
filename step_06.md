# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* step 05

## Step 6 : Vi..vi...Jeg har lavet en fejl.

## Refactoring.

"*Refactoring er en programmeringsteknik, hvor man omskriver en eksisterende programkode således at den bliver enklere og derved lettere at læse, mens dens funktionalitet bevares. Ideen med at gøre koden mere læsevenlig er at koden også bliver lettere at forstå, lettere at holde ved lige og lettere at videreudvikle.*"

Ja, man skulle *næsten* tro det var meningen :).

Men jo vi skal lave lidt Refactoring = Re-fak-tu-re-ring.

Problemer:

1. jeg har kaldt vores endpoints `/users` hvor det skulle være `/user`.
2. jeg har kaldet filen `users.js` hvor det skulle være `user.js`.

Løsning.

### 1. `server.js`

1. Vi laver vi alle 4 `routes` om fra `'/users'` til `'/user'`
2. Vi retter referencen i toppen til `const user = require('./user');`


### 2. `users.js`

1. Vi omdøber `users.js`til `user.js`
2. Vi retter modulet til at hedde `const user = {}` og retter de steder `users` nu skal være `user` istedet.
3. Husk også nederst i export.

Det skulle være det. Og hvorfor skulle vi så gøre det?

Det handler om ordentlig god struktur. Alle vores endpoints arbejder med én bruger.
Hvis vi gerne vil have logiske endpoints til at slette flere brugere, så giver det mere mening at man kalder et "users" endpoint, altså i flertal. 

Og husk det når i prøver fra `postman` vi har ændret `endpoint` til `/user`.

## Afslutning Step 06.

Vi holder det til refakturering i denne opgave.

I step 7 går vi til replikering...*næsten*



