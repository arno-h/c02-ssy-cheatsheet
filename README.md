# Kurz-Referenz für SSY-LV

## NPM
* `npm install` ... installiert die Dependencies (die Module die zum Ausführen benötigt werden)
* `npm run start` ... startet die Applikation, nachträgliche Code-Änderungen werden nicht nachgeladen. Die Applikation ist dann über http://localhost:3000/ erreichbar.
* `npm run monitor` ... startet die Applikation, bei Code-Änderungen wird die Anwendung neu gestartet

## JavaScript

* **Variable**
  * `let x = 3` ... veränderliche Variable (später `x = 4` setzen möglich)
  * `const x = 3` ... unveränderliche Variable (spätere Zuweisung nicht mehr möglich)
* **Array**
  * `[1, 2, 3]` ... Definition eines neuen Arrays
  * `[]` ... leeres Array
  * `arrVar.length` ... Länge eines Arrays
  * `arrVar.push(var)`  ... fügt *var* am Ende des Arrays *arrVar* hinzu
  * `arrVar.shift()` ... entfernt erstes Element aus Array und retourniert dieses
  * `arrVar.includes(var)` ... *true*/*false*, ob die Variable im Array enthalten ist
  * `arrVar.sort([fun])` ... sortiert das Array; falls Funktion *fun* übergeben,
                             dann sortiert anhand der Return-Werte (`<0`,`0`,`>0`) von *fun*
  * `arrVar.map(fun)`  ... führt die Funktion *fun* für jedes einzelne Element des Arrays aus
* **Objekt** (bzw. Key-/Value-Map)
  * `{ key1: "Wert1", key2: 7, key3: true }` ... Definition eines neuen Objekts
  * `{}` ... leeres Objekt
  * `var.key` ... mit Punkt auf Methoden und Attribute zugreifen
  * `var[key]` ... alternativ mit Array-Notation auf Attribute zugreifen
  * `delete var.key` ... Wert aus Objekt entfernen/löschen
  * `Object.keys(var)` ... retourniert Array aller Keys des Objekts
* **Einige Tests**:
  * Existiert eine Variable? `typeof var !== "undefined"`
  * Array leer? `arrVar.length === 0`
  * Existiert Objektattribut (Key)? `"key" in var` oder `typeof var.key !== "undefined"`
* **Schleifen**:
  * `for (let i = 0; i < arrVar.length; i++) { let value=arrVar[i]; ... }`
  * `for (let value of arrVar) { ... }` ... weist den Wert direkt der Variable zu
  * `for (let key in object) { let value=object[key] }` zur Iteration über Objekt-Attribute (Keys)
    (Gefährlich: listet auch geerbte Properties/Keys auf!)
  
### Verschachtelung

Manchmal ist es notwendig, Variablen über Request-Grenzen hinweg weiterzureichen.
Allerdings kann bei der Response-Funktion kein zusätzlicher Parameter mitübergeben werden.
Die Lösung ist, die Funktionen ineinander zu verschachteln:

```javascript
function func1(param1) {
    let var1 = 1;
    func2();      // ohne Parameter

    function func2() {  // verschachtelt
        let var2 = 2;
        func3();  // ohne Parameter

        function func3() {  // weiter verschachtelt
            // hier Zugriff auf alle Variable möglich
            console.log(param1, var1, var2);
            // Achtung: Falls func1, func2 oder func3 gleiche Parameternamen haben
            // dann wird letzte Definition verwendet --> verwenden Sie unterschiedliche
            // Namen in allen Funktionen, um Fehler zu verhindern
        }
    }
}
```

(Anmerkung: Da bei größeren Programmen die Verschachtelung stark zunehmen kann, haben sich im NodeJS-Ökosystem
einige alternative Techniken etabliert: async, Promises etc. Für unsere Beispiele haben wir aber so
gut wie nie eine größere Verschachtelungstiefe, weshalb wir diese zusätzlichen Bibliotheken nicht verwenden.)


## JSON

Javascript ist die Muttersprache von JSON, weshalb die Notation auch so ähnlich aussieht.
Wesentlicher Unterschied: Bei JSON werden die Keys zwingend in Anführungszeichen >"< gesetzt.

* `let var = JSON.parse(string)` ... dekodiert einen String und retourniert den Wert
                                     (kann Objekt, Array, Bool, String etc. sein)
* `let string = JSON.stringify(var)` ... erzeugt einen JSON-String aus einer Variable 


## LokiJS-Datenbank

* **Basics**
  * `db.getCollection('xyz')` ... Referenz auf Kollektion (Tabelle) holen
  * `collection.find()` ... Alle Einträge aus Kollektion auslesen
  * `collection.get(12)` ... Eintrag mit ID=12 auslesen
  * `collection.insert(var)` ... Eintrag in Kollektion einfügen (erhält dann DB-Meta-Attribute)
  * `collection.remove(var)` ... Eintrag auf Kollektion löschen
    (muss Objekt selbst sein, keine ID → vorher mit `.get()` Objekt aus DB holen)
  * `collection.update(var)` ... Eintrag einer Kollektion aktualisieren (Objekte vorher mit `get()` auslesen)
* **Find**
  * `collection.find({key: value, key2: value2, ...})` ... Liefert alle Objekte
    zurück, bei denen *key = value* und *key2=value2* ist.
  * `collection.find({'key': { '$ne' : 'value' }});` ... Liefert alle Objekte
    zurück, bei denen *key != value* ist. `$ne`
  * `collection.find({'key': {'$gt': 40}})` ... Liefert alle Objekte
    zurück, bei denen *key > 40* ist. `$gt`
  * `collection.find({'key': {'$gte': 40}})` ... Liefert alle Objekte
    zurück, bei denen *key >= 40* ist. `$gte`
  * `collection.find({'key': {'$lt': 40}})` ... Liefert alle Objekte
    zurück, bei denen *key < 40* ist. `$lt`
  * `collection.find({'key': {'$lte': 40}})` ... Liefert alle Objekte
    zurück, bei denen *key <= 40* ist. `$lte`
  * `collection.find({ 'key' : { '$between': [50, 75] }})` ... Liefert alle Objekte
    zurück, bei denen *key zwischen 50 und 75* ist. `$between`
  * `collection.find({ 'key' : { '$in' : ['value', 'value2'] } })` ... Liefert alle Objekte
    zurück, bei denen *key value oder value2* ist. `$in`
  * `collection.find({ 'key' : { '$nin' : ['value', 'value2'] } })` ... Liefert alle Objekte
    zurück, bei denen *key **nicht** value oder value2* ist. `$nin`
* **Where**
  * `collection.where(function)` ... Liefert alle Objekte zurück, bei denen *function* *true* retourniert;
    Beispiel:
    ```javascript
    collection.where(hat4Beine);
    function hat4Beine(obj) { return (obj.beine == 4); }
    ```


## Request 

* **GET/DELETE**: 
  ```javascript
  Request.get(urlVar, responseFunction);
  // In der Reponse-Funktion muss der body mit JSON.parse() eingelesen werden

  // Alternativ (dann wird body automatisch geparst)
  Request.get({
      url: urlVar,
      json: true
    },
    responseFunction);
  ```
* **POST/PUT/PATCH**: (wenn also Daten mitübergeben werden müssen)
  ```javascript
  Request.put({
      url: urlVar,
      json: jsonVar
    },
    responseFunction);
  ```
* **Response**-Funktion:
  ```javascript
  function xyResponse(error, response, body) {
      // falls body JSON enthält (und nicht automatisch geparst wurde)
      let dataObject = JSON.parse(body);
    
      // Status-Code der Antwort auslesen, z.B. auf 200 testen:
      response.statusCode === 200
  }
  ```
  
  
## Express: Routen

* Anlegen:
  ```javascript
  router.get('/:varName', routeFunction);
  router.post("/", routeFunction);
  ```
* Routen-Funktion
  ```javascript
  function routeFunction(request, response) {
    let x = request.params.varName;   // selber Name wie in Route
    let y = request.query.queryName; // für http://...?queryName=123
    let z = request.body.bodyName;   // für JSON-Daten im Body
    ...
    response.json(result); // zum Senden einer JSON-Antwort
    ...
    response.status(301);  // zum Setzen des HTTP-Status
    ...
    response.status(204).end();  // zum Senden einer leeren Antwort
  }
  ```
