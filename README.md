# Kurz-Referenz für SSY-LV

## NPM
* `npm install` ... installiert die Dependencies (die Module die zum Ausführen benötigt werden)
* `npm start` ... startet die Applikation, nachträgliche Code-Änderungen werden nicht nachgeladen.
  Die Applikation ist dann über http://127.0.0.1:3000/ erreichbar.
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
  * `var.key` ... mit Punkt auf Methoden und Attribute zugreifen (*key* ist ein *Name*)
  * `var[key]` ... alternativ mit Array-Notation auf Attribute zugreifen (*key* ist *Variable*, die Namen enthält)
  * `delete var.key` ... Wert aus Objekt entfernen/löschen
  * `Object.keys(var)` ... retourniert Array aller Keys des Objekts
* **Einige Tests**:
  * Existiert eine Variable? `typeof var !== "undefined"` oder `var === undefined`
  * Array leer? `arrVar.length === 0`
  * Existiert Objektattribut (Key)? `"key" in var` oder (wie oben) `var.key === undefined`
* **Schleifen**:
  * `for (let i = 0; i < arrVar.length; i++) { let value=arrVar[i]; ... }`
  * `for (let value of arrVar) { ... }` ... weist den Wert direkt der Variable zu
  * `for (let key in object) { let value=object[key] }` zur Iteration über Objekt-Attribute (Keys)
    (Gefährlich: listet auch geerbte Properties/Keys auf!)
* **Mathematik und Strings**:
  * `Math.max(a,b)` ... Maximum von *a* und *b*
  * `Math.min(a,b)` ... Minimum von *a* und *b*
  * `Math.round(a)` ... gerundete (Integer-)Zahl
  * `parseFloat(strVar)` ... retourniert eine Gleitkommazahl
  * `parseInt(strVar)` ... retourniert eine Ganzzahl
* **Zeitverzögerung**
  * `setTimeout(func, msec, arg1, arg2, ...)` ... ruft die Funktion *func* in *msec* Millisekunden mit den
    Parametern *arg1*, *arg2* etc. auf. Auf Variablen aus der Umgebung (siehe Verschachtelung unten) kann
    weiterhin zugegriffen werden.


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
    let y = request.query.queryName;  // für http://...?queryName=123
    let z = request.body.bodyName;    // für JSON-Daten im Body
    ...
    response.json(result); // zum Senden einer JSON-Antwort
    ...
    response.status(301);  // zum Setzen des HTTP-Status
    ...
    response.status(204).end();  // zum Senden einer leeren Antwort
  }
  ```


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
  * `collection.find({key: value, key2: value2, ...})` ... Liefert *alle* Objekte
    zurück, bei denen *key=value* und *key2=value2* ist. Ergebnis ist ein *Array*.
  * `collection.findOne({key: value, key2: value2, ...})` ... Liefert *ein*
    Objekt zurück, bei dem *key=value* und *key2=value2* ist. Ergebnis ist
    *null* (nichts gefunden) oder der erste/einzige Treffer.
* **Where**
  * `collection.where(function)` ... Liefert alle Objekte zurück, bei denen *function* *true* retourniert;
    Beispiel:
    ```javascript
    collection.where(hat4oderMehrBeine);
    function hat4oderMehrBeine(obj) { return (obj.beine >= 4); }
    ```


## HTTP-Requests mit Axios

HTTP-Requests werden im JavaScript-Ökosystem asnychron verarbeitet.
D.h. der Programmablauf fährt wie gewohnt fort, obwohl der Request bzw. insbesondere die
Response noch nicht erhalten wurde. Stattdessen werden **Callbacks** (Funktionen, die
aufgerufen werden, sobald die Response erhalten worden ist) verwendet. Callbacks sind
aber in größeren Programmen unpraktisch, weshalb **Promises** (Versprechen) 
und **async/await** in die Sprache eingeführt wurden.

### Promise

  ```javascript
  axios.get(url)
    .then(function(response) {
      // wird ausgeführt, sobald Response erhalten wurde
      ...
    })
    .catch(function(error) {
      // wird im Fehlerfall ausgeführt (z.B. HTTP 404)
      ...
    })
    .finally(function () {
      // wird in beiden Fällen ausgeführt (zum Schluss)
      ...
    })
  ```

Es ist auch möglich, mehrere Requests parallel abzusetzen:

  ```javascript
  Promise.all([
        axios.get(url1),
        axios.get(url2),
        axios.get(url3),
    ])
    .then(function(response) { ... })
    .catch(function(error) { ... })
    .finally(function () { ... })
  ```

### async/await


  ```javascript
  async function xy() {
    try {
        const response = await axios.get(url);
        // an dieser Stelle ist Response verfügbar
    } catch(error) {
      // wird im Fehlerfall ausgeführt (z.B. HTTP 404)
    } finally {
      // wird immer zum Schluss ausgeführt
    }   
  }
  ```

### Requests

* **GET/DELETE**: 
  ```javascript
  axios.get(url, options);
  axios.delete(url, options);
  // options sind optional. Sie können z.B. URL-Query-Parameter enthalten,
  // als Alternative zum manuellen Anhängen der Parameter in der URL:
  // axios.get(url, { params: { ... } })
  ```
* **POST/PUT/PATCH**: (wenn also Daten mitübergeben werden müssen)
  ```javascript
  axios.post(url, data, options);
  axios.put(url, data, options);
  axios.patch(url, data, options);
  // data ist ein String oder ein gewöhnliches JavaScript-Objekt
  // options sind optional
  ```
* **Response**:
  Die wichtigsten Felder in der Response sind:
  * `response.status`: HTTP-Code, z.B. 200, 404
  * `response.statusText`: HTTP-Statustext, z.B. "OK", "Not found"
  * `response.data`: der Body der Response (gleich als Javascript-Objekt)
  * `response.config`: die ursprünglichen Daten des Requests (z.B. `config.url`)
* **Error**:
  Die wichtigsten Felder im Error-Objekt sind alle in `error.response` enthalten.
  Es hat die selben Felder wie in der Response oben beschrieben,
  z.B. `error.response.status` oder `error.response.data`.
 
