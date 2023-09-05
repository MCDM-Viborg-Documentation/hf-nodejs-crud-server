# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* step 04

## Step 5 : Mere Schema og Mongo Queries.

I step 4 tilføjede vi en email adresse som det sidste punkt.

Og den eneste vi skulle var at udvide vores model og sende email´en med i vores payload = body.

```JavaScript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    firstname: {type: String},
    surname: {type: String},
    email: {type: String},
    age: {type: Number},
    street : {type:String},
    zip : {type:Number},
})

module.exports = mongoose.model('user', userSchema);
```

Men lad os gøre vores håndtering lidt mere optimal.

Hvis vi kigger på vores ``users.create`` funktion i ``users.js``.

Så lad os lave den lidt om. Lad os tage højde for at vores database kan aflevere en fejl besked hvis noget ikke går som forventet. 

Så nu opretter vi vores egen besked ``message`` og ``data`` som indeholder det data vi sender tilbage.

```JavaScript
users.create = (payload, callback) => {

    console.log('users.create payload:', payload);

    User.create(payload).then( (user) => {

        callback(200, {'message' : 'Bruger er oprettet', 'data' : user});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugeren kunne ikke oprettes', 'data' : err});
    
    });

};
```

Hvis du opretter en bruger nu vil du se noget ligende og derfor ingen forskel, alt går godt:
```JavaScript
{
    "message": "Bruger er oprettet",
    "data": {
        "firstname": "HANS",
        "surname": "Christensen",
        "email": "adsdasds@asdasd.dk",
        "age": 32,
        "street": "Gyden",
        "zip": 8680,
        "_id": "64f734649aa190c9ba3779f0",
        "__v": 0
    }
}
```

Som brand nye mongo *server-database-ejere* har vi et ansvar for at gemme solid data. Og når vi laver en klient `<form></form>` så validere vi input felterne og sørger for at vi ikke sender en form med mangelfuld data. Men som server ejer skal vi alligevel også på server siden sørge for at man ikke kan gemme med mangelfuld data, med mindre det er data som ikke er "required".

Og inden vi begiver os ud i et væld af if/else løsninger, så lad os udnytte at vi via vores User Schema, kan tage os af en del "up-front".

Den nye ``users.create`` funktion har nu en ``catch`` der fanger eventuelle fejl og vi udskriver en besked og selve fejlen.

Åben nu for din ``user.model.js`` fil. Prøv at ændre din email til at indholde dette objekt.

```JavaScript
email: {type: String, required: true, unique: true},
```
Kig godt på den, hvad forventer vi?

Prøv at oprette en bruger og se retur beskeden i PostMan.

1. Prøv uden udfyldt email.
2. Prøv med den samme email 2 gange.
3. Og prøv så med en ny email adresse.

Som I kan se, så får vi en fejlbesked retur og i den fejlbesked kan vi se hvad der er galt.     
KeyPattern = email      
KeyValue = morten@mail.dk


```JSON 
{
    "message": "Brugeren kunne ikke oprettes",
    "data": {
        "index": 0,
        "code": 11000,
        "keyPattern": {
            "email": 1
        },
        "keyValue": {
            "email": "morten@mail.dk"
        }
    }
}
```

Vi bør ikke sende den fejl-besked da vi er ikke interreseret i at fortælle vores bruger at mailen er i brug. Men vi gør det lige nu så vi kan lære at læse fejlene.

Der er mange muligheder med Schema som gør livet lettere at leve.

Tilføj denne til din ```user.model.js`` Schema:

```JavaScript
isMember : {type: Boolean , default: false},
```

Prøv nu at opret en ny bruger, med det samme payload som vi plejer at bruge.

Den bruger vi opretter vil nu have en ``Boolean (true/false)``property der hedder ``isMemeber`` og den vil være sat til ``false`` som default værdi.

Vi vil gerne vide hvem der er tilmeldt vores medlemskab og hvem der bare er oprettet og npr vi opretter vores bruger har de ikke valgt om de vil være medlem endnu. (bare et simpelt eksempel).

Når en hjemmeside skal trække på vores data så er `user objektet` som det skal være i forhold til vores data arkitektur.

Prøv igen, at læse lidt af https://mongoosejs.com/docs/validation.html for at se de mange muligheder der er for at validere dit indhold. Fordelen er helt klart at du sikre din data meget tidligt i processen og ikke skal lave en masse logik i din ``users.create`` kode for at undersøge om du kan oprette, slette, opdatere osv.

Vi skal i størst muligt omfang få data til at flyde som vand og ikke have en masse hjørner der skal slibes før den kan afleveres. Så derfor er det ikke nok at validere på backend man gør dt også på frontenden og dermed "spilder" vi ikke unødige kræfter på at sende data vi dybest set ved ikke vil bliver godkendt. 

Derfor sender man ikke en form uden en valid email, eller de felter vi ved er påkrævet. Ellers skal ``serveren/backenden/databasen/`` arbejde for at fortælle det uundgåelige -> "Brugeren kunne ikke oprettes" / "Der skete en fejl". 

Og hvis hvert kald til databasen koster penge, hvis alt trafik koster penge så kan vi spare mange kroner ved at undgå unødvendigt fejl data.

Det kommer vi til at arbejde med og da vi er arkitekterne bestemmer vi hvordan data´en skal være.

For lige at fuldende vores nuværende applikation så lad os hente brugerne fra databasen istedet for den fil vi tidligere oprettede.

### Mongo Query

Når man skal hente data fra mong skal man benytte en Query.

"*a computer language used to make queries into databases and information systems*".

En query en en forespørgsels object som beskriver hvad man søger efter.

Se denne liste:
https://mongoosejs.com/docs/queries.html

Som I kan se står der f.eks ``Model.find()`` - det giver næsten sig selv at man ønsker at *finde* noget på *modellen*. Så oversat til vores brugere er det ``User.find()`` da ``User`` er vores model.

I `find()` metoden skal vi så tilføje vores `Query` - Den vi skal bruge for at hente alle vores `users` er meget let for den er tom. Dvs: ``User.find({})`` vil lede efter alle brugere, som i pricippet *bare* er et objekt, og det er alle `users` pr. definition. Så med den simple Query får vi alle.

Så prøv at ændre ``users.read`` metoden til:

```JavaScript
users.read = (callback) => {

    User.find({}).then( (users) => {

        callback(200, {'message' : 'Brugeren Hentet', 'data' : users});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugeren kunne ikke hentes', 'data' : err});
    
    });
    
};
```

Læg mærke til, at vi benytter ``User`` modellen igen, vi kalder metoden ``find()``. Vi tildeler ``find()`` metoden en tom ``query``, altså et tomt objekt ``{}`` således: ``find({})``.

Resultatet er et array af ``users`` som vi udskriver i vores callback metode.

Prøv nu i `postman` at lave et `GET` imod `http://localhost:3000/users` og resultat skulle gerne være en liste af brugere og en besked om at de er hentet.

Nu har vi klaret to af vores punkter i C.R.U.D projektet. Vi ``create`` og ``read``.

Nu mangle vi bare ``update`` og ``delete`` så lad os lige kigge på listen igen.

https://mongoosejs.com/docs/queries.html

Vi ser blandt andet:
```JavaScript
Model.deleteOne()
Model.updateOne()
```

Det lyder *meget* som noget vi kan bruge, vi vil gerne kunne slette én og opdatere én.

Så skal vi bare fortælle hvilken vi gerne vil slette. Vi benytter `email` som vores `nøgle` i dette system. Så hvis man skal slette en bruger skal det være med email adressen.

På samme måde benytter vi email adressen til at opdatere - Det er ikke muligt at ændre sin email adresse for nuværende. 

*(*Email-Nøgle : måske ikke det mest hensigtsmæssige men det bliver ofte brugt og hver email i systemet skal være unikt - og det er nemmere for os at huske imens vi lære, og vi kan ændre eller kombinere med _id.*).

Lad os slette en bruger.

Vi starter med vores ``endpoint`` vi har brug for at tilføje endnu et til at fortælle at vi ønsker at slette. Til det høre ``delete`` metoden.

Så lad os åbne ``server.js``.

og indsæt efter vores `read` endpoint:

```JavaScript
expresServer.delete('/users', (req, res) => {

    users.delete(req.body, (code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });
    
});
```

Læg mærke til, at dette ``delete`` endpoint ligner vores ``post`` endpoint, der er *kun to små* ændringer.

Vi skriver `delete` istedet for `post` og vi kalder en `delete` metode på `users` istedet for create.

Så lad os hoppe over i `users.js` og oprette `delete` metoden.

Åbn `users.js` og indsæt:

```JavaScript
users.delete = (payload, callback) => {

    console.log('users.delete payload:', payload);
    
    User.deleteOne({"email": payload.email}).then( (result) => {

        callback(200, {'message' : 'Bruger er slettet', 'data' : result});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugeren kunne ikke slettes', 'data' : err});
    
    });

};
```

Læg mærke til, at vores ``users.delete`` metode ikke adskliier sig fra vores ``users.create`` metode, pånær den ``Model`` metode vi kalder: ``Model.deleteOne()``.

Vi tager imod et `payload` vi forventer `payloadet` indeholder en email (*bør vi sikre os*).

Vi benytter `emailen` til at lave en `query`der finder den `user` der matcher emailen.

```JavaScript
{"email": payload.email}
```

Så ``User.deleteOne({"email": payload.email})`` sletter den bruger der har den email vi sender med i vores payload.

Opret nu et nyt `request` i post man, eventuelt en kopi af `post`.

Men nu skal metoden være `DELETE` og `body` behøver bare at være `email`

```Json
{
    "email" : "bruger@mail.dk"
}
```

Når du sender requestet vil brugeren med emailen blive slettet og du vil få en besked ala:
```
{
    "message": "Bruger er slettet",
    "data": {
        "acknowledged": true,
        "deletedCount": 1
    }
}
```

Selv om ´emailen`ikke findes så får du den samme besked, men ``deletedCount`` vil være ``0``.

Så nu har vi tre Punkter nede i vores `C.R.U.D`.

### Update

Lad os nappe den sidste - så har vi "full-circle" og kan *lege* med alle detaljerne.

"*same procedure as last year..*"

Vi starter med et `endpoint`.

åbn `server.js` og indsæt efter `read`og før `delete` metoderne:

```JavaScript
// Server Endpoints (UPDATE)
expresServer.put('/users', (req, res) => {

    users.update(req.body, (code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });
    
});
```

Læg mærke til, Ja ikke sandt! - Det ligner også `post` og `delete`. Her har vi udskifte det til en `PUT` metode og kalder `update` funktionen på `users` modulet.

Så lad os lave en update funktion i `users.js`:

```JavaScript
users.update = (payload, callback) => {

    console.log('users.update payload:', payload);
    
    User.updateOne({ "email": payload.email }, payload).then( (user) => {

        callback(200, {'message' : 'Bruger er opdateret', 'data' : user});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugeren kunne ikke opdateres', 'data' : err});
    
    });

};
```

Læg mærke til, at vi igen lave meget *det samme* men her kigger vi på `updateOne()` metoden.

Her er vores `Query` lidt anderledes `{ "email": payload.email }, payload`.

Se her: https://mongoosejs.com/docs/api/model.html#Model.updateOne().

Vi benytter to Queries i virkeligheden én til at finde den udvalgte og en query med hvad der skal opdateres.

Så fladfodet, hvis vi skulle opdatere en brugers alder vil vores `query` se således ud:

```JavaScript
User.updateOne( { "email": "anders@medieskolerne.dk" }, { age : 12 } )
```

Så vil brugeren med email: anders@medieskolerne.dk få sin alder opdateret til 12. 

I vores udgave sender vi et payload med de felter vi vil ændre, vi kan dog ikke ændre på emailen.

```JavaScript
User.updateOne( { "email": payload.email }, payload )
```

Vi finder vores `user` fra `payload.email` og vi sender hele `payloadet` ind med de felter vi vil have ændret. 

Så opret et `request` i `Postman`, det skal være ligesom et `POST` igen men med metoden `PUT`.

I vores `body` indsætter vi: (*du ændre selvfølgelig til en email på en bruger i din database*)

```Json
{
    "email" : "anders@medieskolerne.dk",
    "isMember" : true
}
```

Send `requestet` og nu skulle brugerens medlemskab gerne være true. Du kan prøve at *vippe* den lidt frem og tilbage true/false.

Hvis vi vil opdatere alderen så kan vi sende:

```Json
{
    "email" : "anders@medieskolerne.dk",
    "age" : 30
}
```

Hvis vi opdatere mere, så sender vi bare mere:

```Json
{
    "email" : "anders@medieskolerne.dk",
    "age" : 30,
    "firstname" : "Hanne",
    "surname" : "Jensen"
}
```

Vi skal bare have `emailen` med hver gang til identifikation i vores `query`.

**Vi kan **IKKE** opdatere med nye properties der ikke er en del af vores schema. 
Du kan lave en objekt der kan indeholde *hvad som helst* og dermed overkomme dette "problem". Men det er ikke nu.! - Og selvfølgelig er der også andre måder at gøre det på - men det er klart med schema er man lidt mere låst, men omvendt er man også mere sikret.*

**Vi er i mål!**

Sådan da. Men Vi har en ægte C.R.U.D på vores hænder. Vi kan Oprette, Læse, Opdatere og Slette.

Så nu kan vi udforske.! 

Men vi starter skidt i Step 6.






















