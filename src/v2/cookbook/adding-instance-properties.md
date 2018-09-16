---
title: Eigenschaften zu Instanzen hinzufügen
type: cookbook
order: 2
---

## Einfaches Beispiel

Es kann vorkommen, dass es Daten oder Routinen gibt, die Sie in mehreren Komponenten verwenden wollen, ohne den [global Scope zu verschmutzen](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md). In diesen Fällen können sie sie jeder Vue-Instanz zur Verfügung stellen, indem Sie sie auf dem Prototyp definieren:

```js
Vue.prototype.$appName = 'My App'
```

Jetzt ist `$appName` auf allen Vue-Instanzen verfügbar, sogar vor ihrer Erstellung. Wenn wir 

```js
new Vue({
  beforeCreate: function() {
    console.log(this.$appName)
  }
})
```

ausführen, wird `"My App"` in die Konsole ausgegeben werden! 

## Warum der Gültigkeitsbereich (Scope) der Instanzen-Eigenschaften wichtig ist

Sie könten sich fragen:

> "Warum beginnt `appName` mit `$`? Ist das wichtig? Was macht es?

Da gibt es keine Zauberei. `$` ist eine Vue-Konvention, die besagt, dass die jeweilige Eigenschaft allen Instanzen zur Verfügung steht. Dadurch werden Konflikte mit definierten Daten, berechneten Eigenschaften und Methoden vermieden.

> "Konflikte?" Was meinen Sie damit?

Noch eine gute Frage! Wenn Sie

```js
Vue.prototype.appName = 'My App'
```

schreiben, was würden Sie erwarten, dass im Beispiel unten ausgegeben wird?

```js
new Vue({
  data: {
    // Autsch - appName ist *auch* der Name einer
    // Instanzeneigenschaft, die wir definiert haben!
    appName: 'The name of some other app'
  },
  beforeCreate: function() {
    console.log(this.appName)
  },
  created: function() {
    console.log(this.appName)
  }
})
```

Zuerst würde `"My App"`, dann `"The name of some other app"` ausgegeben werden, weil `this.appName` durch `data` [mehr oder minder](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md) überschrieben wurde, als die Instanz erstellt wurde. Wir geben den Gültigkeitsbereich der Instanzeneigenschaften mit `$` an, um dies zu verhindern. Sie können sogar eigene Konventionen nutzen, wenn Sie wollen (z. B. `$_appName` oder `ΩappName`). Damit vermeiden Sie Konflikte mit künftigen Plugins oder Funktionalitäten. 

## Realistisches Beispiel: Vue-Ressourcen mit Axios ersetzen

Nehmen wir an, Sie wollen eine [veraltete Vue Ressource](https://medium.com/the-vue-point/retiring-vue-resource-871a82880af4) ersetzen. Sie haben den Zugriff auf Methoden über `this.$http` genossen und wollen das Gleiche mit Axios machen.

Das Einzige, was Sie machen müssen ist, Axios zu Ihrem Projekt hinzuzufügen:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.15.2/axios.js"></script>

<div id="app">
  <ul>
    <li v-for="user in users">{{ user.name }}</li>
  </ul>
</div>
```

Erstellen Sie einen Alias `axios`, der auf `Vue.prototype.$http` verweist:

```js
Vue.prototype.$http = axios
```

Dann werden Sie in der Lage sein, Methoden wie `this.$http.get` in jeder Vue-Instanz aufzurufen:  
```js
new Vue({
  el: '#app',
  data: {
    users: []
  },
  created() {
    var vm = this
    this.$http
      .get('https://jsonplaceholder.typicode.com/users')
      .then(function(response) {
        vm.users = response.data
      })
  }
})
```

## Der Kontext der Prototyp-Methoden

Falls Sie es nicht wissen, bekommen Methoden, die zu einem Prototyp hinzugefügt werden, den Kontext der jeweligen Instanz. Das heisst, sie können `this` verwenden, um auf Daten, berechnete Eigenschaften, Methoden und alle andere Elemente der Instanz zuzugreifen.

Nutzen wir das einmal in der `$reverseText`-Methode:

```js
Vue.prototype.$reverseText = function(propertyName) {
  this[propertyName] = this[propertyName]
    .split('')
    .reverse()
    .join('')
}

new Vue({
  data: {
    message: 'Hello'
  },
  created: function() {
    console.log(this.message) // => "Hello"
    this.$reverseText('message')
    console.log(this.message) // => "olleH"
  }
})
```

Beachten Sie, dass die Kontextbindung **nicht** funktionieren wird, wenn Sie die Pfeilnotation aus ES6/2015 verwenden, da sie implizit an den Gültigkeitsbereich ihres Eltern-Elements bindet. Dass bedeutet, dass der Code

```js
Vue.prototype.$reverseText = propertyName => {
  this[propertyName] = this[propertyName]
    .split('')
    .reverse()
    .join('')
}
```

einen Fehler werfen wird:

```log
Uncaught TypeError: Cannot read property 'split' of undefined
```

## Wann dieses Muster vermieden werden soll

Solange Sie auf den Gültigkeitsbereich der Prototypeneigenschaften aufpassen, ist die Verwendung dieses Musters relativ sicher, d. h. es ist unwahrscheinlich, dass dadurch Fehler produziert werden. 

Jedoch kann es manchmal Verwirrung bei anderen Entwicklern stiften. Sie können z. B. `this.$http` sehen und sich denken "Oh, ich habe diese Vue-Funktionalität nicht gekannt!" Dann gehen sie zu einem anderen Projekt und sind verwirrt, wenn `this.$http` undefiniert ist. Oder sie wollen googeln, wie man etwas macht und finden keine Ergebnisse, weil sie nicht begreifen, dass sie in Wirklichkeit Axios über einen Alias nutzen.

**Der Preis der Bequemlicheit ist die fehlende Ausdrücklichkeit.** Wenn Sie eine Komponente ansehen, ist es unmöglich herauszufinden, woher `$http` kommt. Ist es in Vue eingebaut? Aus einem Plugin? Hat's ein Kollege hinzugefügt?

Was sind also die Alternativen?

## Alternative Entwurfsmuster

### Wenn kein Modulsystem verwendet wird

In Anwendungen **ohne** ein Modulsystem (z. B. WebPack oder Browserify), gibt es ein Entwurfsmuster das oft mit _beliebigen_ JavaScript-erweiterten Frontends verwendet wird: ein globales `App`-Objekt.

Es könnte eine gute Wahl sein, wenn das, was Sie himzufügen wollen, nichts mit Vue an sich zu tun. Hier ist ein Beispiel:

```js
var App = Object.freeze({
  name: 'My App',
  version: '2.1.4',
  helpers: {
    // Dies ist eine rein funktionale Version
    // der $reverseText-Methode, die wir vorhin
    // gesehen haben.
    reverseText: function(text) {
      return text
        .split('')
        .reverse()
        .join('')
    }
  }
})
```
<p class="tip">`Object.freeze` verhindert, dass das Objekt in Zukunft verändert wird. Dieser Aufruf macht alle Eigenschaften zu Konstanten und schützt Sie damit vor Fehlern, die durch die Veränderung des Zustands verursacht werden.
</p>

Jetzt ist die Quelle dieser geteilten Eigeschaften offensichtlicher: es gibt ein `App`-Objekt, das irgendwo in der Anwendung definiert ist. Um es zu finden, können die Entwickler eine projektweite Suche durchführen.

Ein anderer Vorteil ist, dass `App` jetzt _überall_ in Ihrem Code verwendet werden kann, unabhängig davon, ob es Vue-bezogen ist oder nicht. Unter anderem können Sie Werte direkt an Instanzenoptionen anfügen, statt eine Funktion für den Zugriff auf `this`-Eigenschaften zu verwenden:
 
```js
new Vue({
  data: {
    appVersion: App.version
  },
  methods: {
    reverseText: App.helpers.reverseText
  }
})
```
### Wenn ein Modulsystem verwendet wird

Wenn Sie Zugang zu einem Modulsystem haben, können Sie geteilten Code mittels Module organisieren. Dann können Sie mit `require`/`import` diese Module dort verwenden, wo Sie sie brauchen. Das ist der Inbegriff der Eindeutigkeit, weil Sie in jeder Datei eine Liste der Abhängigkeiten haben. Sie wissen _exakt_, wo jede von ihnen herkommt.

Obwohl dieser Ansatz wortreicher ist, ist er zugleich definitiv am Besten zu warten, besonders, wenn Sie mit anderen Entwickler zusammenarbeiten und/oder eine große Anwendung schreiben.
