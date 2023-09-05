# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel node server og opretter via endpoint brugere i en Mongo Database.
```

## Forudsætning.

* step 06

## Step 7 : Produkter.

Nu skal vi se om vi kan forstå "systemet" vi har fået opbygget.

Så før vi skal arbejde mere med `Queries` skal vi lige oprette endnu en omgang crud.

Vi skal oprette for et meget simpelt produkt.

## Produkt Objekt.
{
    "title" : "Produkt titel",
    "price" : 110,
    "recommended" : false,
    "discountInPercent" : 0
}

*Krav til Model Schema* 

1. `title` og `price` er påkrævet.
2. `title`er unik. - der kan kun være én.
2. `recommended` er default `false`.
3. `discountInPercent` kan kun være imellem 0 og 100.
4. Produktet `opdateres`, `slettes` med `_id`.

*krav til struktur*
Vi opretter endpoints og metoder til `/product`, `product.js`.

*krav til løsning*
Man skal kunne `(c)reate`, `(r)ead`, `(u)pdate`, `(d)elete` produkter fra postman.

## Afslutning Step 07.

God Arbejdslyst!





