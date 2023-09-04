# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```


## Forudsætning.

* Step 1

## Step 2

Det er klart at vi løber hensynsløst over en masse dybde i ``nodeJS`` men vi gør det fordi det er sjovere at få noget til at virke, for derefter at stille spørgsmål. Samtidigt skal vi bruge det til at blive klogere. Så vi sigter med denne øvelse efter, hurtigts muligt at få "hul" på a til z.

Altså at vi kan skrive, hente, opdatere og slette objekter fra vores backend data. Herefter kan vi igennem dette udvide vores forståelse ved rent faktisk at have noget at "lege" med, som kan bruges til noget.

## Users.

Idéen med denne server er at vi skal kunne skrive, hente, opdatere og slette en række "brugere".

Til det får vi brug for et ``users`` endpoint vi kan kalde. ```http://localhost:3000/users```

C.R.U.D Står for ``create``, ``read``, ``update`` og ``delete``.

Som metoder kaldes det ``post``, ``get``, ``put`` og ``delete``.

Lad os starte med den letteste, at læse ``read`` med metoden ``get``.

Vi opretter et ``users`` ``endpoint``:

```JavaScript
// Server Endpoints (READ)
expresServer.get('/users', (req, res) => {

    res.setHeader('Content-Type', 'application/json; charset=utf-8');
    res.send({"hello" : "data"});
    
});
```

Nu sender vi et ``respons`` med et ``json`` content header.
Derfor sender vi også json data tilbage til modtageren.

Nu kan vi teste det i PostMan ved at kalde ```http://localhost:3000/users``` med en ``POST`` metode.
Resultate vil være:
```Json
{
    "hello":"data"
}
```

Så nu er det i princippet bare at sende en liste med de brugere der er oprettet i vores system.

F.eks. et array af bruger objekter.
```Json
[
    {
        "_id" : 0,
        "firstname" : "Peter",
        "surname" : "Hansen",
        "age" : 23,
        "street" : "Borgervej 178",
        "zipcode" : 2150
    },
    {
        "_id" : 1,
        "firstname" : "Henning",
        "surname" : "Mortensen",
        "age" : 18,
        "street" : "Hirsevej 12",
        "zipcode" : 8000
    },
    {
        "_id" : 2,
        "firstname" : "Karen",
        "surname" : "Jensen",
        "age" : 33,
        "street" : "Møllevej 8",
        "zipcode" : 7210
    }
]
```

Lad os oprette denne data i en ``json`` fil.

Opret en mappe i roden af projektet og kald den ``data``.
opret en fil det hedder ``data.json`` og indsæt ovenståenede array.

Udskift nu vores ``res.send({"hello" : "data"});`` med ``sendFile()`` og som med vores client forside sender vi nu vores data fil direkte tilbage til forspørgeren ``requestet``. 

```JavaScript
// Server Endpoints (READ)
expresServer.get('/users', (req, res) => {

    res.setHeader('Content-Type', 'application/json; charset=utf-8');
    res.sendFile(path.resolve(__dirname, '../data/data.json'));
    
});
```

Test det i ``PostMan`` og se indholdet af din ``data.json`` fil. ``http://localhost:3000/users``.

## Simpelt?, både og.

Dette er meget forsimplet - men hvis det er fordi vi bare skal læse et json dokument, ja så er det sådan set rigeligt.

Men vi skal jo både skrive, løse osv. Og i virkeligheden er det nødvendigvis ikke kun det vores server skal gøre, der kunne komme mage behov og derfor endpoints og håndtering af disse. Så vi får brug for lidt code-splitting. 

Derfor opretter vi en ``users.js`` fil i ``lib`` mappen. I den vil vi håndtere alt user data manipulation.

Vi opretter ``users`` som en ``CommonJS`` modul:

```JavaScript
const fs = require('fs');
const users = {};

users.read = (callback) => {

    fs.readFile('./data/data.json', 'utf8', (err, data) => {

        callback(200, data);

    });
};

module.exports = users;
```

Læg mærke til af vi i denne fil ``require`` et ``nodeJS`` modul, ``fs`` som er en forkortelse for  file-system. Altså vores fil system.

Vi benytter denne til at gå i vores data mappe og hente ``data.json`` filen.

Det underlige ved denne metode er ``callback``. ``callback`` er et meget benytte ``pattern/mønster`` i ``CommonJS``. Det kaldes for ``callback-pattern`` og er i virkeligheden en form for ``promise``.

Vi arbejder med det i vores lille backend her for at få lidt kendskab til håndværket bag mange node webservere og da vi mest skal arbejde med at gemme data via MongoDB så skader det ikke at vi arbejder lidt med CommonJS.

Med her er ``callback`` i virkeligheden en funktion som skal kaldes med to parametere.

Det betyder, at når vi har fået vores ``request`` og sendt det videre til vores ``users.js`` fil så forventer vi at vi kalder callback funtionen med to parametere. 

Så lad os prøve at ændre ``server.js`` så vi kalder ``read`` metoden i ``user.js`` med en ``callback`` funktion med to parameter.

Åbn server.js

Insæt reference til user.js filen.
```
const users = require('./users');
```

Herefter ændre ``get`` metoden til:
```JavaScript
expresServer.get('/users', (req, res) => {

    users.read( (code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });

});
```

Nu kalder vi ``read`` metoden på ``users`` modulet. Vi kalder den med en funktion `` (code, returnObj) => {}`` så vi forventer at når den funktion bliver kaldt, så er det med to parameter en code og et objekt.

F.eks
```
callback(200, {"hello" : "data"});
```

For når vi kigger på vores ``read`` metode så tager vi imod funktionen som ét parameter vi kalder callback og hvis vi var meget præcise kunne vikalde de callbackFunction.

Dette koncept kan være lidt svært at greje til at starte med.

Så her er de to funktioner der benytter ``callback`` imellem hianden.

server.js
```JavaScript

    users.read((code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);
    });
```

user.js
```JavaScript
users.read = (callback) => {

    fs.readFile('./data/data.json', 'utf8', (err, data) => {

        callback(200, data);

    });
};
```

serveren får requestet og kalder på users.read metoden med en funktion (code, returnObj). ``user.js`` læser filen og kalder ``callback`` metoden med de to parameter "udfyldt", eg 200 og data fra filen.

Så kan ``server.js`` sende en `header`, en `status kode 200` fordi, alt er gået godt og tilsidst ``returnObj = return objektet``, i dette tilfælde et `json` array af user objekter. 

Og dette ``callback`` pattern vil vi så benytte igen og igen for alle vores endpoints, stort set.

Fordelen er at vi nu i vores ``user håndtering kan aflevere forskellige retur objekter og forskellige koder alt efter om vores arbejde med dataen gå godt, er tilgængelig, kræver authentication osv.

## Afslutning Step 02.

Nu kan vi gå flere veje.

Vi kunne vælge at gemme i json filer på vores server og vi kan vælge at gemme i database.

Vi vælger i første omgang at gemme i MongoDB det er mest hensigtsmæssig da det gør vores arbejde nemmere og samtidigt får en masse andre muligheder som vil være enormt tidskrævene at skabe selv.

Backup, indeksering, query-muligheder for at slå vores data op osv osv argumenterne er mange.

Men der er tilfælde hvor vi sagtens kan nøjes med at læsen en lille fil. Det kunne være configuartions variabler eller et navigations setup som bare skal styres via lidt json. 

Det handler i virkeligheden om behov omkring det enkelte produkt og omstændighederne.

Step 03 kræver MongoDB Community Edition.
















