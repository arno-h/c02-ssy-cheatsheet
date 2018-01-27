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
  * `var.key` ... mit Punkt auf Methoden und Attribute zugreifen
  * `var.key = undefined` ... Wert aus Objekt entfernen/löschen

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
einige alternative Techniken etabliert: async, promises etc. Für unsere Beispiele haben wir aber so
gut wie nie eine größere Verschachtelungstiefe, weshalb wir diese zusätzlichen Bibliotheken nicht verwenden.)


## LokiJS-Datenbank

* **Basics**
  * `db.getCollection('xyz')` ... Referenz auf Kollektion (Tabelle) holen
  * `collection.find()` ... Alle Einträge aus Kollektion auslesen
  * `collection.get(12)` ... Eintrag mit ID=12 auslesen
  * `collection.insert(var)` ... Eintrag in Kollektion einfügen (erhält dann DB-Meta-Attribute)
  * `collection.remove(var)` ... Eintrag auf Kollektion löschen
    (muss Objekt selbst sein, keine ID → vorher mit `.get()` Objekt aus DB holen)
  * Werden nur einzelne Werte eines Objektes aktualisiert (das mit `get()` geholt wurde), ist kein *save* oder *update* notwendig.
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
