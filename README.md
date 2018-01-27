# Kurz-Referenz für SSY-LV

## JavaScript

* **Variable**
  * `let x = 3` ... veränderliche Variable (später `x = 4` setzen möglich)
  * `const x = 3` ... unveränderliche Variable (spätere Zuweisung nicht mehr möglich)
* **Array**
  * `[1, 2, 3]` ... Definition eines neuen Arrays
  * `[]` ... leeres Array
  * `arrVar.push(var)`  ... fügt *var* am Ende des Arrays *arrVar* hinzu
  * `arrVar.shift()` ... entfernt erstes Element aus Array und retourniert dieses
  * `arrVar.includes(var)` ... *true*/*false*, ob die Variable im Array enthalten ist
  * `arrVar.sort([fun])` ... sortiert das Array; falls Funktion *fun* übergeben, dann sortiert anhand der Return-Werte (`<0`,`0`,`>0`) von *fun*
  * `arrVar.map(fun)`  ... führt die Funktion *fun* für jedes einzelne Element des Arrays aus
* **Objekt** ()bzw. Key-/Value-Map)
  * `{ key1: "Wert1", key2: 7, key3: true }` ... Definition eines neuen Objekts
  * `var.key1` ... mit Punkt auf Methoden und Attribute zugreifen


## LokiJS-Datenbank

* **Basics**
  * `db.getCollection('xyz')` ... Referenz auf Kollektion (Tabelle) holen
  * `collection.find()` ... Alle Einträge aus Kollektion auslesen
  * `collection.get(12)` ... Eintrag mit ID=12 auslesen
  * `collection.insert(var)` ... Eintrag in Kollektion einfügen (erhält dann DB-Meta-Attribute)
  * `collection.remove(var)` ... Eintrag auf Kollektion löschen
    (muss Objekt selbst sein, keine ID → vorher mit `.get()` Objekt aus DB holen)
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
      let dataObject = JSON.parse(body); // falls body JSON enthält
      ...
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
  }
  ```
