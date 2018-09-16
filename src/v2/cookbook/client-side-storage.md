---
title: Clientseitige Datenspeicherung
type: cookbook
order: 11
---

## Grundlegendes Beispiel

Clientseitige Datenspeicherung ist eine exzellente Möglichkeit, die Performance einer Anwendung zu verbessern. Indem Sie die Daten im Browser selber speichern, können Sie auf das Holen der Daten vom Server (wann immer der Benutzer die Daten braucht) verzichten. Diese Funktionalität ist im Offline-Modus besonders nützlich. Jedoch werden sogar Online-Nutzer davon profitieren, dass die Daten lokal und nicht auf einem entfernten Server gespeichert werden. Clientseitige Datenspeicherung kann mittels [Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies), [Local Storage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) (lokale Speicerhung, technisch "Web-Speicherung"), [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) und [WebSQL](https://www.w3.org/TR/webdatabase/) (eine veraltete Methode, die nicht in neuen Projekten eingesetzt werden soll).

In diesem Kochrezept werden wir uns auf die lokale Speicherung konzentrieren. Sie ist die einfachste von allen. Lokale Speicherung verwendet ein Schlüssel-Wert-System zum Speichern von Daten. Es können nur einfache Werte gespeichert werden. Sie können jedoch komplexere Datenstrukturen speichern, wenn Sie bereit sind, diese von und nach JSON zu kodieren (dekodieren). Im Allgemeinen gilt: Lokale Speicherung ist geeignet für kleinere Datenmengen, die Sie dauerhaft speichern wollen, z. B. Benutzereinstellungen oder Formulardaten. Größere und komplexere Daten sind normalerweise in IndexedDB besser aufgehoben.

Beginnen wir mit einem einfachen Formular-basierten Beispiel:

``` html
<div id="app">
  My name is <input v-model="name">
</div>
```

Dieses Beispiel hat ein Formularfeld, welches zu einem Vue-Wert namens `name` gebunden ist. Hier ist das zugehörige Java-Script-Code:

``` js
const app = new Vue({
  el: '#app',
  data: {
    name: ''
  },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
  },
  watch: {
    name(newName) {
      localStorage.name = newName;
    }
  }
});
```

Betrachten Sie die `mounted`- und `watch`-Teile. Wir verwenden `mounted`, um das Laden der Wert aus dem lokalen Speicher umzusetzen. Wir verfolgen die Änderungen des Wertes `name` und schreiben den Wert in den lokalen Speicher, sobald sich `name` verändert hat.

Hier können Sie diesen Code selber laufen lassen:

<p data-height="265" data-theme-id="0" data-slug-hash="KodaKb" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/KodaKb/">testing localstorage</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Geben Sie etwas in das Formular ein und laden Sie die Seite neu. Sie werden merken, dass der zuvor eingegebene Wert im Formular automatisch aufscheint. Vergessen Sie nicht, dass Ihr Browser exzellente Entwicklertools zur Verfügung stellt, mit denen Sie den client-seitigen Datenspeicher inspiziern können. Hier ist ein Beispiel aus Firefox:

![Entwicklertools für Datenspeicher in Firefox](/images/devtools-storage.png)

Und hier in Chrome:

![Entwicklertools für Datenspeicher in Chrome](/images/devtools-storage-chrome.png)

Und, schließlich, ein Beispiel aus Microsoft Edge. Beachten Sie, dass Sie den Datenspeicher der Anwendung unter der Registerkarte "Debugger" finden können.

![Storage devtools in Edge](/images/devtools-storage-edge.png)

<p class="tip">Kurze Nebenbemerkung: Diese Entwicklertools erlauben Ihnen auch, Werte aus dem Datenspeicher zu entfernen. Beim Testen kann das sehr hilfreich sein.</p>

Es ist nicht immer ratsam, die Werte unmittelbar nach ihrer Veränderung zu speichern. Schauen wir uns ein etwas fortgeschritteneres Beispiel an. Zunächst, das aktualisierte Formula.

``` html
<div id="app">
  <p>
    My name is <input v-model="name">
    and I am <input v-model="age"> years old.
  </p>
  <p>
    <button @click="persist">Save</button>
  </p>
</div>
```

Jetzt haben wir zwei Felder (wieder gebunden an eine Vue-Instanz), aber jetzt haben zusätzlich eine Schaltfläche, bei deren Betätigung die Methode `persist` ausgeführt wird. Sehen wir uns den JavaScript-Code an.

``` js
const app = new Vue({
  el:'#app',
  data: {
    name: '',
    age: 0
  },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
    if (localStorage.age) {
      this.age = localStorage.age;
    }
  },
  methods: {
    persist() {
      localStorage.name = this.name;
      localStorage.age = this.age;
      console.log('now pretend I did more stuff...');
    }
  }
})
```

Wie früher, wird die `mounted`-Methode verwendet, um gespeicherte Daten zu laden, falls diese existieren. Diesmal speichern wir die Daten nur, wenn die Schaltfläche angeklickt wird. Wir könnten hier auch Überprüfungen oder Umwandlungen vornehmen, bevor wir den Wert speichern. Sie könnten hier auch das Datum speichern, an dem die Werte gespeichert wurden. Anhand dieser Information könnte die `mounted`-Methode entscheiden, ob die Daten erneut gespeichert werden sollen oder nicht (wie in der Version unten). Sie können diese Version unten ausprobieren.

<p data-height="265" data-theme-id="0" data-slug-hash="rdOjLN" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/rdOjLN/">testing localstorage 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Arbeit mit komplexen Werten

Wie zuvor erwähnt, funktioniert der Lokalspeicher nur mit einfachen Werten. Um komplexere Werte wie Objekte oder Arrays zu speichern, müssen diese in JSON serialisiert und deserialisiert werden. Hier ist ein fortgeschrittenes Beispiel, in dem ein Katzenarray (das Beste aller möglichen Arrays) gespeichert wird.

``` html
<div id="app">
  <h2>Cats</h2>
  <div v-for="(cat, n) in cats">
    <p>
      <span class="cat">{{ cat }}</span>
      <button @click="removeCat(n)">Remove</button>
    </p>
  </div>

  <p>
    <input v-model="newCat">
    <button @click="addCat">Add Cat</button>
  </p>
</div>
```

Diese "Anwendung" besteht aus einer einfachen Liste im oberen Teil (mit einem einer Schaltfläche zum Entfernen einer Katze) und einem kleinen Formular unten, mit dem neue Katzen hinzugefügt werden können. Sehen wir uns jetzt den JavaScript-Code an:

``` js
const app = new Vue({
  el: '#app',
  data: {
    cats: [],
    newCat: null
  },
  mounted() {
    if (localStorage.getItem('cats')) {
      try {
        this.cats = JSON.parse(localStorage.getItem('cats'));
      } catch(e) {
        localStorage.removeItem('cats');
      }
    }
  },
  methods: {
    addCat() {
      // Hier stellen wir sicher, dass der Benutzer tatsächlich etwas eingetippt hat
      if (!this.newCat) {
        return;
      }

      this.cats.push(this.newCat);
      this.newCat = '';
      this.saveCats();
    },
    removeCat(x) {
      this.cats.splice(x, 1);
      this.saveCats();
    },
    saveCats() {
      let parsed = JSON.stringify(this.cats);
      localStorage.setItem('cats', parsed);
    }
  }
})
```

In dieser Anwendung verwenden wir das Lokalspeicher-API statt dem "direkten" Zugang. Beide Optionen funktionieren, aber der API-Methode wird meistens der Vorzug gegeben. Die `mounted`-Methode muss nun den Wert nehmen und den JSON-Wert parsen. Wenn hier etwas schief läuft, dann nehmen wir an, dass die Daten beschädigt sind und entfernen diese. (Bedenken Sie, dass der Benutzer Zugang zu den lokal gespeicherten Daten hat und diese jederzeit verändern kann.)

Wir haben jetzt drei Methoden, die die Arbeit mit einer Katze betreffen. Sowohl `addCat`, als auch `removeCat` aktualisieren die "live"-Vue-Daten, die in `this.cats` gespeichert sind. Dann rufen sie `saveCats` auf. Diese Methode serialisiert und speichert die Daten. Sie können mit dieser Version unten spielen.

<p data-height="265" data-theme-id="0" data-slug-hash="qoYbyW" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="localstorage, complex" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/qoYbyW/">localstorage, complex</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Alternative Entwurfsmuster

Obwohl das Lokalspeicher-API relativ simpel ist, fehlen dort einige grundlegende Funktionalitäten, die in vielen Anwendungen nützlich wären. Die folgenden Plugins vereinfachen die Arbeit mit dem Lokalspeicher und fügen neuen Funktionalitäten wie Default-Werte hinzu.

* [vue-local-storage](https://github.com/pinguinjkeke/vue-local-storage)
* [vue-reactive-storage](https://github.com/ropbla9/vue-reactive-storage)

## Fazit

Obwohl der Browser ein Speichersystem auf dem Server niemals vollständig ersetzen wird, können die die unterschiedlichen Möglichkeiten der lokalen Zwischenspeicherung die Performance stark verbessern. Vue.js macht die Arbeit mit diesem Werkzeug noch leistungsfähiger.
