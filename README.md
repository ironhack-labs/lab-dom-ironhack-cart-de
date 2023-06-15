![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# LAB | Ironhack Cart

![giphy (2)](https://user-images.githubusercontent.com/76580/167435963-34b5ddf0-e318-446a-b59f-2edeed3eb030.gif)

## Einführung

E-Commerce hat sich im 21. Jahrhundert als großer Game-Changer in der Wirtschaft erwiesen. Als einer der größten Verkaufskanäle, nur hinter dem physischen Einzelhandel, [wird erwartet](https://www.statista.com/statistics/379046/worldwide-retail-e-commerce-sales/), dass E-Commerce bis zum Jahr 2024 für weltweite Verkäufe in Höhe von 6,3 Billionen USD verantwortlich sein wird.

E-Commerce ist ein stark umkämpftes Geschäft, und eine positive Benutzererfahrung ist entscheidend, um Kunden zu halten und die Konversion zu verbessern. Es ist nicht ungewöhnlich, dass Unternehmen erhebliche Investitionen tätigen, um den Kaufprozess auf ihren E-Commerce-Plattformen zu optimieren.

Einer der wichtigsten Bestandteile dieser Erfahrung ist der **Warenkorb**.

In diesem Lab werden wir den **IronCart** bauen, einen Warenkorb für den inoffiziellen Ironhack-Merchandising-Shop. Besucher sollten in der Lage sein, Produkte zum Warenkorb hinzuzufügen und zu entfernen und die Anzahl (Menge) der Artikel, die sie kaufen möchten, zu ändern. Zusätzlich sollten Benutzer in der Lage sein, die Zwischensumme und den Gesamtpreis für die hinzugefügten Artikel zu sehen.

## Anforderungen

- Fork dieses Repo.
- Clone es auf Deine Maschine.



## Abgabe

- Nach Abschluss führe die folgenden Befehle aus:

```
bash $ git add .$ git commit -m "Solved lab"$ git push origin master  
```    
- Erstelle einen Pull Request, damit Deine TAs Deine Arbeit überprüfen können.





## Anleitung
Du wirst die meiste Arbeit in der Datei `js/index.js` durchführen. Wir haben bereits das Markup in `index.html` und einige grundlegende Styles hinzugefügt. Stelle beim Entwickeln sicher, dass Du dieselben Klassennamen verwendest wie bereits vorhanden (und im CSS-File verfügbar), damit unser Warenkorb schön und sauber aussieht.

Los geht's!

<br>    
 <hr>    

### Hinweis zu Tests
Dieses LAB ist mit Unit-Tests ausgestattet, um automatisiertes Feedback über Deinen Fortschritt im Labor zu geben.

**Nach Abschluss der grundlegenden Iterationen** gehe zum Abschnitt **"Teste Deinen Code"** am Ende des Dokuments. Dort wirst Du aufgefordert, die Test-Abhängigkeiten zu installieren und die Tests auszuführen, um zu prüfen, wie viele Tests Dein Code besteht. Sobald Du die Tests ausgeführt hast, korrigiere Deinen Code, um die fehlgeschlagenen Tests zu bestehen.

<hr>    
 <br>    


### Iteration 1: `updateSubtotal`
Öffne `index.html` in Deinem Browser. Wie Du sehen kannst, gibt es nur eine Zeile in der Tabelle, die ein Produkt repräsentiert. In dieser ersten Iteration wirst Du Dich nur auf dieses Produkt konzentrieren und später werden wir Dir helfen, Möglichkeiten zu finden, um auf mehrere Produkte zu aktualisieren.

Werfen wir einen Blick auf den bereitgestellten HTML-Code. Wir haben das **table-Tag mit der ID `#cart`**, wie unten dargestellt:

```html <table id="cart">    
  <thead>    
  <tr>    
  <th>Product Name</th>    
 <th>Unit Price</th>    
 <th>Quantity</th>    
 <th>Subtotal</th>    
 <th>Action</th>    
  </tr>    
  </thead>    
 <tbody>  <tr class="product">    
 <!-- ... -->  </tr>    
  </tbody>    
<!-- ... --></table>
```   
![](https://i.imgur.com/zCWQYg2.png)

Das eine Produkt, das wir derzeit in unserem `#cart` haben, befindet sich in der `tr` mit der Klasse `product` (**die innerhalb von `tbody` liegt**):

```html <tr class="product">    
  <td class="name">    
  <span>Ironhack Rubber Duck</span>    
  </td>    
 <td class="price">$<span>25.00</span></td>    
 <td class="quantity">    
  <input type="number" value="0" min="0" placeholder="Quantity" />    
  </td>    
 <td class="subtotal">$<span>0</span></td>    
 <td class="action">    
  <button class="btn btn-remove">Remove</button>    
</td> </tr> 
```   

Das Produkt hat einen **Preis** und eine **Menge** (wobei die Menge angibt, wie viele Artikel eines bestimmten Produkts ein Benutzer zum Warenkorb hinzugefügt hat). Im bereitgestellten Code sehen wir auch einen **Zwischensummenpreis**. Der Zwischensummenpreis ergibt sich aus der Multiplikation dieser Werte.

Dein Ziel ist es, den Zwischensummenpreis zu berechnen, aber lass uns schrittweise vorgehen:

- **Schritt 0**:  In diesem Schritt ist unser Ziel, Dir zu helfen, den bereitgestellten Code in `js/index.js`. Dank des bereitgestellten Codes hat der Button `Calculate Prices` bereits eine Funktionalität. Durch die DOM-Manipulation haben wir das Element mit der `id="calculate"` abgerufen und einen Event Listener  `click` hinzugefügt. Wenn dieser Button angeklickt wird, wird die Funktion  `calculateAll()` ufgerufen. Der folgende Codeausschnitt tut genau das, was wir erklärt haben:

```javascript // js/index.js    
 window.addEventListener('load', () => {    
const calculatePricesBtn = document.getElementById('calculate'); calculatePricesBtn.addEventListener('click', calculateAll);}); 
```   
Verwirre dich nicht mit der Methode [.addEventListener()](https://www.w3schools.com/jsref/met_document_addeventlistener.asp) sie tut genau dasselbe wie [onclick()](https://www.w3schools.com/tags/ev_onclick.asp), mit einigen Unterschieden, über die du hier mehr erfahren kannst [hier](https://stackoverflow.com/questions/6348494/addeventlistener-vs-onclick). In diesem Lab kannst du die Methode verwenden, die du bevorzugst.

Okay, lass uns zur `calculateAll()` Funktion gehen. In dieser Funktion haben wir `querySelector` verwendet, um das erste (und derzeit einzige) DOM-Element mit der Klasse `product` zu erhalten. Dieses Element (das wir in der Variable `singleProduct`) gespeichert haben) wird als Argument an die Funktion `updateSubtotal()` übergeben. Wie du in den Kommentaren finden kannst, wird der bereitgestellte Codeausschnitt nur zu Testzwecken im Iteration 1 verwendet.

```js function calculateAll() {    
 // code in the following two lines is added just for testing purposes. // it runs when only iteration 1 is completed. at later point, it can be removed. const singleProduct = document.querySelector('.product'); updateSubtotal(singleProduct); // end of test    
// ITERATION 2 //... // ITERATION 3 //...} 
```   
Und schließlich haben wir die `updateSubtotal(product)` Funktion gestartet. Im Moment tut diese Funktion nur eins: Sie gibt eine Meldung aus `Calculate Prices clicked!`, wenn auf die Schaltfläche _Calculate Prices_ geklickt wird.

![](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_e50868b669d962f3ddb26802e5a16638.gif)

Los geht's:

- **Schritt 1**: Erstelle innerhalb von `updateSubtotal()`, zwei Variablen (wir schlagen vor, sie `price` und `quantity` zu nennen) und verwende deine gerade erworbenen DOM-Manipulationsfähigkeiten, um die DOM-Elemente abzurufen, die Preis und Menge halten. Um dir einen Vorsprung zu geben, könntest du den folgenden Code verwenden, um das DOM-Element, das den `price` hält, abzurufen::

```js // js/index.js function updateSubtotal(product) {    
const price = product.querySelector('.price span'); // ... your code goes here} 
```   
- **Schritt 2**: Jetzt, da du die oben genannten DOM-Elemente erhalten hast, sollte dein nächster Schritt sein, die spezifischen Werte daraus zu extrahieren. _Hinweis_: Vielleicht erinnert dich `innerHTML` daran? Falls du neugierig bist, andere Möglichkeiten zu finden, um das gleiche Ergebnis zu erzielen, kannst du damit beginnen, [`textContent`](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) und [`innerText`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText) in Google zu überprüfen. Außerdem kannst du den Wert aus einer Eingabe extrahieren, indem du auf die [Eigenschaft `value` des Eingabeelements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#htmlattrdefvalue) zugreifst. ass dich hier jedoch nicht ablenken, das soll dein _zusätzliches Lesen_ sein, wenn du das Lab abgeschlossen hast.

- **Schritt 3**: Verwende die aus den oben genannten DOM-Elementen extrahierten Werte, um den Subtotalpreis zu berechnen. Du kannst eine neue Variable erstellen, und ihr Wert entspricht dem Produkt dieser Werte. Zum Beispiel, wenn ein Benutzer 3 Ironhack-Gummienten auswählt, sollte er sehen, dass der Zwischensummenwert 75 Dollar beträgt ($25 * 3 = $75).

- **Schritt 4**: Jetzt, wo du ein DOM-Manipulations-Ninja wirst, verwende deine Fähigkeiten noch einmal, um das DOM-Element abzurufen, das die Zwischensumme enthalten sollte. _Hinweis_: Es ist das Element mit der Klasse `subtotal`.

- **Schritt 5**: In Schritt 3 hast du den Zwischensummenpreis berechnet und in Schritt 4 das DOM-Element erhalten, das diesen Preis anzeigen sollte. In diesem Schritt ist dein Ziel, den Zwischensummenpreis auf das entsprechende DOM-Element zu setzen. Stelle außerdem sicher, dass diese Funktion den Wert der Zwischensumme zurückgibt, damit du ihn später bei Bedarf verwenden kannst.

In dieser Iteration hast du die Funktion `updateSubtotal` erstellt, die **die Zwischensumme** für dieses spezifische Produkt **berechnet**, den **Zwischensummenwert** für dasselbe Produkt im DOM **aktualisiert** und den **Zwischensummenwert** zurückgibt.

Als einziger Argument sollte die Funktion ein **DOM-Element** entgegennehmen, das einem einzelnen `tr`-Element mit der Klasse `product` entspricht. Im Boilerplate-Code haben wir es `product` genannt.

```js function updateSubtotal(product) {    
// ...} 
```   
:bulb: _Hinweis_: Stelle sicher, dass deine `calculateAll()`-Funktion den zuvor erwähnten Testcode enthält. Wenn der Code an seinem Platz ist, nachdem du die `updateSubtotal()`-Funktion erfolgreich abgeschlossen hast, solltest du in der Lage sein, die entsprechenden Aktualisierungen im `Subtotal`-Feld in der Tabelle zu sehen.

Schau [hier](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_30b87c596b79954f63b3482d2f320fe4.gif) das erwartete Ergebnis an.

<hr>   

### Iteration 2: `calculateAll()`

Unser aktueller Code funktioniert perfekt für ein Produkt, aber wir erwarten, dass mehr als ein Produkt in unserem Warenkorb vorhanden ist. Deshalb werden wir `calculateAll` verwenden, um die Aktualisierung der Zwischensummen für jedes Produkt auszulösen.

Vervollständige die Funktion namens `calculateAll()`. Ihr Zweck besteht darin, die Funktion `updateSubtotal` für jedes `tr.product` DOM-Element im `table#cart` aufzurufen.

Um zu beginnen, entferne oder kommentiere den vorhandenen Code innerhalb von `calculateAll()` aus (den Code, den wir für Testzwecke verwendet haben). Fügen wir auch ein neues Produkt zu unserer `index.html`-Datei hinzu. Dupliziere das `tr` mit der Klasse `product`, benenne das Produkt darin um und ändere den Produktpreis.

![](https://i.imgur.com/Pv4NmR8.png)

:bulb: _Hinweis_: Beginne damit, die DOM-Knoten für jede Produktzeile zu erhalten. Derzeit haben wir zwei Produkte; daher haben wir zwei Zeilen mit der Klasse `product`. Möglicherweise kann hier [getElementsByClassName](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByClassName) gut dienen.

```js 
function calculateAll() {    
// ...} 
```   
Das endgültige Ergebnis sollte wie folgt aussehen:

![](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_0efb56fc0e5717469417e806fa7cde12.gif)

<hr>   

### Iteration 3: ### Gesamtsumme


Unsere Berechnungsfunktionalität ist noch unvollständig. Die Zwischensumme für jedes Produkt wird aktualisiert, aber der Gesamtwert bleibt unverändert.

Am Ende der `calculateAll()`-Funktion kannst du den Gesamtwert verwenden, den du gerade in der vorherigen Iteration berechnet hast, und das entsprechende DOM-Element aktualisieren. Berechne den Gesamtpreis für die Produkte in deinem Warenkorb, indem du alle Zwischensummen addierst, die von `updateSubtotal()` zurückgegeben wurden, als es für jedes Produkt aufgerufen wurde.

Zuletzt zeige diesen Wert in deinem DOM an.

![](https://i.imgur.com/SCtdzMd.png)

<hr>   

## Bonus-Iterationen

### Iteration 4: Entfernen eines Produkts

Benutzer sollten in der Lage sein, Produkte aus ihrem Warenkorb zu entfernen. Zu diesem Zweck hat jede Produktzeile in unserer Tabelle eine Schaltfläche "Entfernen" am Ende.

Aber lasst uns unser Problem Schritt für Schritt lösen. Beginne damit, innerhalb der vorhandenen Funktion, die du an `window.addEventListener()` übergibst, das Dokument nach allen "Entfernen"-Schaltflächen abzufragen, iteriere durch sie und füge jedem eine `click`-Event-Listener-Funktion hinzu, die eine benannte Funktion `removeProduct` als Rückrufargument übergibt. Wenn du einen Hinweis dazu benötigst, schau einfach, wie wir es mit dem Hinzufügen eines Event Listeners auf die `calculatePricesBtn` gemacht haben.

Wir haben bereits `removeProduct(event)` deklariert und etwas Startercode hinzugefügt. Nachdem du mit dem Abfragen der Entfernenschaltflächen und dem Hinzufügen des `click`-Event-Listeners darauf fertig bist, öffne die Konsole und klicke auf eine `Entfernen`-Schaltfläche.

Wie wir sehen können, erwartet `removeProduct(event)` das Ereignis als einzelnes Argument, und das wird das Entfernen des entsprechenden Produkts aus dem Warenkorb auslösen.

:bulb: Tipp: Um auf das Element zuzugreifen, auf dem ein Ereignis ausgelöst wurde, kannst du `event.currentTarget` verwenden. Um einen Knoten aus dem DOM zu entfernen, musst du auf seinen übergeordneten Knoten zugreifen und [`removeChild`](https://developer.mozilla.org/en-US/docs/Web/API/Node/removeChild) darauf aufrufen. Du kannst auf den Elternknoten eines DOM-Knotens von seiner Eigenschaft `parentNode` zugreifen.

Stelle sicher, dass der Preis entsprechend aktualisiert wird, wenn du Produkte aus dem Warenkorb entfernst.

Klicke [hier](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_17b1e9e4d2606239163dddbc5b2a3d9f.gif), um das erwartete Ergebnis zu sehen.

<hr>   

### Iteration 5: Neue Produkte erstellen

Um es abzuschließen, werden wir dem Benutzer erlauben, ein benutzerdefiniertes Produkt zu ihrem Warenkorb hinzuzufügen.

Kommentiere das `tfoot`-Element und dessen Kinder in der `index.html`-Datei aus:

```html <table>    
  <tbody>    
 <!-- ... -->  </tbody>    
<!-- <tfoot> <tr class="create-product"> <td> <input type="text" placeholder="Product Name" /> </td> <td> <input type="number" min="0" value="0" placeholder="Product Price" /> </td> <td></td> <td></td> <td> <button id="create" class="btn">Create Product</button> </td> </tr> </tfoot> --></table> 
```   
![](https://i.imgur.com/J8aserm.png)

Die beiden Eingabefelder innerhalb des `tfoot` repräsentieren den Namen für das neue Produkt und den Stückpreis, jeweils. Die Schaltfläche "Produkt erstellen" sollte ein neues Produkt zum Warenkorb hinzufügen, wenn sie ausgelöst wird.

Füge einen `click`-Event-Handler zur "Produkt erstellen"-Schaltfläche hinzu, der eine Funktion mit dem Namen `createProduct` als Rückruf aufruft.

In `createProduct` solltest du die DOM-Knoten für die Name- und Stückpreis-Eingabe suchen, ihre Werte extrahieren, eine neue Zeile zur Tabelle mit dem Produktnamen und dem Einzelpreis sowie der Mengeneingabe und der "Entfernen"-Schaltfläche hinzufügen und sicherstellen, dass alle Funktionalitäten wie erwartet funktionieren.

Denke daran, dass das neue Produkt unauffällig aussehen und sich wie jedes zuvor im Warenkorb enthaltene Produkt verhalten sollte. Daher sollte man in der Lage sein, dessen Zwischensumme zu berechnen, wenn die Schaltfläche "Alle berechnen" angeklickt wird, und das Produkt entfernen.

Wenn die Erstellung des Produkts abgeschlossen ist, leere bitte die Eingabefelder im Erstellungsformular.

Klicke [hier](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_00abbd15326ec24d93147196024458f6.gif), um das erwartete Ergebnis zu sehen.

<br>   


## Teste deinen Code

Wir werden wieder mit automatisierten Tests arbeiten!

Öffne bitte dein Terminal, wechsle in das Stammverzeichnis des Labs und führe `npm install` aus, um den Test-Runner zu installieren. Jetzt kannst du den Befehl `npm run test:watch` ausführen, um automatisierte Tests im _Watch-Modus_ auszuführen. Öffne die resultierende `lab-solution.html`-Datei mit der "Live Server"-Erweiterung von VSCode, um immer die Testergebnisse zu sehen.

```
bash $ cd lab-dom-ironhack-cart$ npm install$ npm run test:watch  
```   


Wenn du die Tests für weitere Details überprüfen möchtest, findest du sie in der Datei `tests/ironhack-cart.spec.js`.



<br>   
**Viel Spaß beim Coden! 💙**