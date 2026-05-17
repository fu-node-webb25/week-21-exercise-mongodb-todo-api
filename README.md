# Vecka 21 : Todo API - Veckans Code Review Uppgift

Denna uppgift går ut på att bygga ett praktiskt taget komplett Todo API från grunden där vi inkorporerar allt vi gått igenom hittills i kursen. Vissa delar av förra veckans uppgift går säkert att återanvända, men jag skulle nog rekommendera att ni skapar nya projekt från grunden och eventuellt klistrar in tidigare skriven kod då förra veckans lösning garanterat innehåller en hel del "onödig" kod nu.

APIet skall byggas för en frontendapplikation där användare kan registrera sig och logga in, samt skapa egna personliga Todolistor. 

## Databaser

Innan ni börjar så kan ni med fördel skapa en databas innehållandes 3 st *collections*: **Keys**, **Users**, samt **Todos**. Er *keys*-collection kan ni manuellt stoppa in 5 st API-nycklar i. De andra två listorna kommer ni kunna fylla med data med hjälp av insomnia när ni testar ert API. Notera i exemplen nedan att jag skapat upp ett `userId` utöver `_id`. Detta är något jag brukar göra då jag tycker att MongoDBs `_id` är stora och otympliga, så jag skapar ett andra id med hjälp av ``uuid``. Detta är 100% valfritt och ni kan lika gärna köra på orginal-ID:t (`_id`) om ni hellre vill det.

### Keys

Era nycklar skall se ut på följande sätt:
```
{
  "_id": "68276c10a32353a345491624",
  "key": "todo-77gpj"
}
```

### Users

Era users skall se ut på följande sätt:
```
{
  "_id": "68277d622b50533c2958d5d4",
  "username": "theodor",
  "password": "nyberg14",
  "userId": "b7f6c"
}
```

### Todos

Era todos skall se ut på följande sätt:
```
{
  "_id": "6827978c353abbda6f0302ee",
  "task": "Köpa kattmat",
  "done": true,
  "userId": "b7f6c",
  "createdAt": "2025-05-16T19:52:44.650Z",
  "updatedAt": "2025-05-16T20:03:31.774Z"
}
```

## Dokumentation

Nedan finner du dokumentation för det API du skall bygga:

### Keys

---

**Metod:** ``GET``

**URL:** ``/api/keys``

**Beskrivning:** Returnerar en slumpmässigt utvald API-nyckel.

---

### Auth

Då vi ännu inte börjat leka med tokens så kan ni med fördel i er kod använda `global`-objektet för att sätta en user i er kod: ``global.user = user``. Detta är INTE okej att göra i verkligheten, men tills vi börjar arbeta med tokens så är det okej i utbildningssyfte.

---

**Metod:** ``POST``

**URL:** ``/api/auth/login``

**Body:** ``{ "username" : [username], "password" : [password] }``

**Beskrivining:** Tar emot data i requestets body, jämför mot databasen och returnerar status baserat på utfall.

---

**Metod:** ``POST``

**URL:** ``/api/auth/register``

**Body:** ``{ "username" : [username], "password" : [password] }``

**Beskrivining:** Tar emot data i requestets body, kontrollerar att användaren inte redan finns, och returnerar status.

---

### Todos

---

För alla nedanstående anrop krävs att man skickar med en API-nyckel som query parameter, ex. ``/api/todos?key=todo-hebdg``, eller i headers som `x-api-key`.

**Metod:** ``GET``

**URL:** ``/api/todos``

**Beskrivining:** Returnerar alla todouppgifter som finns i databasen för den inloggade användaren.

---

**Metod:** ``POST``

**URL:** ``/api/todos``

**Body:** ``{ "task" : [task] }``

**Beskrivining:** Detta anrop kan endast göras om en användare är inloggad, annars returneras ett fel. Vid lyckat anrop så skapas en ny todo upp i databasen som kopplas ihop med den inloggade användaren genom dess användar-ID.

---

**Metod:** ``PATCH``

**URL:** ``/api/todos/:todoid``

**Beskrivining:** Detta anrop kan endast göras om en användare är inloggad, annars returneras ett fel. Vid lyckat anrop så togglas statusen "done" mellan ``true/false``. En inloggad användare kan dessutom endast ändra status på sina egna todos.

---

**Metod:** ``DELETE``

**URL:** ``/api/todos/:todoid``

**Beskrivining:** Detta anrop kan endast göras om en användare är inloggad, annars returneras ett fel. Vid lyckat anrop så raderas motsvarande todo. En inloggad användare kan dessutom endast ta bort sina egna todos.

## Validering

I samband med att ni skapar upp era modeller så definerar ni era scheman så att era users och todos överensstämmer med datan som skickas i req.body. Även key behöver ett schema.

## Middlewares

Middlewares kan ni slänga på mot slutet, och följande skall finnas i din lösning:

### authenticateKey

Denna middleware säkerställer att en giltig API-nyckel skickas med till de routes som kräver en API-nyckel.

### authorizeUser

Denna middleware säkerställer att det finns en inloggad användare för de routes som kräver detta.

### errorHandler

Denna middleware fångar upp alla kastade errors och returnerar fel enligt nedanstående: 
```
{
  "message": "Unauthorized user",
  "success": false
}
```

