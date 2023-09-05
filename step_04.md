# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* step 03

## Step 4 : Mongoose & Modeller og Scheme.

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

Så i vores 