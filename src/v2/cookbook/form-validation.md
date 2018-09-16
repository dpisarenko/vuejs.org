---
title: Formularvalidierung
type: cookbook
order: 3
---

## Grundlegendes Beispiel

Formularvalidierung wird nativ vom Browser unterstützt. Manchmal handhaben bestimmte Browser dieses Thema so, dass man sich darauf nicht verlassen kann. Selbst wenn die Validierung perfekt unterstützt wird, können spezifische Anpassungen manchmal notwendig sein. In diesen Fällen kann eine manuellere, Vue-basierte besser geeignet sein. Beginnen wir mit einem einfachen Beispiel.

In einem Formular mit drei Feldern, mache zwei obligatorisch. Schauen wir uns zuerst den HTML-Code an:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
>

  <p v-if="errors.length">
    <b>Korrigiere bitte den/die folgenden Fehler:</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <label for="age">Alter</label>
    <input
      id="age"
      v-model="age"
      type="number"
      name="age"
      min="0">
  </p>

  <p>
    <label for="movie">Lieblingsfilm</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Abschicken"
    >
  </p>

</form>
```

Gehen wir das Beispiel von oben nach unten durch. Das `<form>`-Tag hat eine ID, die wir für die Vue-Komponente verwenden werden. Es gibt einen Ereignishandler für die Schaltfläche "Abschicken", die Du gleich sehen wirst. `action` ist eine temporäre URL, die auf etwas Reales auf irgendeinem Server verweisen würde (wo Du natürlich eine serverseitige Backup-Validierung hast).

Darunter steht ein Absatz, das sich je nach Fehlerzustand ein- und ausblendet. Das wird eine einfache Liste von Fehlern im oberen Teil des Formulars rendern. Beachte auch, dass wir die Validierung beim Abschicken des Formulars auslösen und nicht bei Änderung eines Feldes.

Das Letzte, was Du beachten sollst, ist dass jedes der drei Felder ein zugehöriges `v-model` hat. Diese verbinden die Felder mit den Werten, mit denen wir in JavaScript arbeiten werden. Sehen wir uns das an.

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    age: null,
    movie: null
  },
  methods:{
    checkForm: function (e) {
      if (this.name && this.age) {
        return true;
      }

      this.errors = [];

      if (!this.name) {
        this.errors.push('Der Name ist ein Pflichtfeld.');
      }
      if (!this.age) {
        this.errors.push('Das Alter ist ein Pflichtfeld.');
      }

      e.preventDefault();
    }
  }
})
```

Ziemlich kurz und einfach. Wir definieren ein Array, um die Fehler zu speichern und setzen die Werte dreier Felder auf `null`. Die `checkForm`-Logik (welche beim Abschicken des Formulars ausgeführt wird) prüft nur den Namen und das Alter, weil der Lieblingsfilm optional ist. Wenn sie leer sind, prüfen wir jedes davon und setzen einen spezifischen Fehler für jeden von ihnen. Und das war's. Du kannst das Demo unten laufen lassen. Vergiss nicht, dass beim erfolgreichen Abschicken, eine POST-Anfrage an eine temporäre URL geschickt wird.

<p data-height="265" data-theme-id="0" data-slug-hash="GObpZM" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 1" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/GObpZM/">form validation 1</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Verwenden benutzerdefinierter Validierung

Im zweiten Beispiel wird das zweite Feld (Alter) zur E-Mail-Adresse umfunktioniert. Dieses Feld wird mit einem Stück benutzerdefinierter Logik validiert. Der Code wurde aus einer Frage auf StackOverlow [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript) genommen. Das ist eine wunderbare Frage, weil verglichen mit Diskussionen darin die ärgsten politischen/religiösen Debatten auf Facebook wie eine leichte Dissonanz darüber, wer das beste Bier macht, aussehen. Es geht dort wirklich verrückt zu. Hier ist der HTML-Code, obwohl er sich vom ersten Beispiel nicht stark unterscheidet:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Korrigiere bitte den/die folgenden Fehler:</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
    <input
      id="name"
      v-model="name"
      type="text
      name="name"
    >
  </p>

  <p>
    <label for="email">E-Mail-Adresse</label>
    <input
      id="email"
      v-model="email"
      type="email"
      name="email"
    >
  </p>

  <p>
    <label for="movie">Lieblingsfilm</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Abschicken"
    >
  </p>

</form>
```

Es gibt hier nur wenige Veränderungen. Beachte den Coe `novalidate="true"` am Anfang des Formulars. Es ist wichtig, weil der Browser versuchen wird, die E-Mail-Adresse zu validieren, weil wird dort `type="email"` angegeben haben. Ehrlich gesagt, macht es in diesem Fall mehr Sinn, dem Browser zu vertrauen. Aber da wir ein Beispiel mit der benutzerdefinierten Validierung haben wollen, werden wir das ausschalten. Hier ist der aktualisierte JavaScript-Code:

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    email: null,
    movie: null
  },
  methods: {
    checkForm: function (e) {
      this.errors = [];

      if (!this.name) {
        this.errors.push("Der Name ist ein Pflichtfeld.");
      }
      if (!this.email) {
        this.errors.push('Die E-Mail-Adresse ist ein Pflichtfeld.');
      } else if (!this.validEmail(this.email)) {
        this.errors.push('Die E-Mail-Adresse ist ungültig.');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    },
    validEmail: function (email) {
      var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    }
  }
})
```

Wie Du sehen kannst, haben wir `validEmail` als eine neue Methode hinzugefügt und sie wird einfach aus `checkForm` aufgerufen. Du kannst mit diesem Beispiel hier spielen:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqNXZ" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqNXZ/">form validation 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Anderes Beispiel für eine benutzerdefinierte Validierung

Für das dritte Beispiel haben wir etwas gebaut, was Du vermutlich in Umfrage-Anwendungen gesehen hast. Der Benutzer wird aufgefordert, ein "Budget" für eine Ansammlung von Funktionalitäten für ein neues Sternenzerstörer-Modell auszugeben. Der Gesamtbetrag muss 100 ergeben. Zuerst der HTML-Code:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Korrigiere bitte den/die folgenden Fehler:</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    Angenommen, Du hast ein Budget von 100 Dollar. Gebe an,
	wieviel Du für die folgenden Funktionalitäten der
	nächsten Generation eines Sternenzerstörers ausgeben
	würdest. Dein Gesamtbetrag muss gleich 100 sein.
  </p>

  <p>
    <input
      v-model.number="weapons"
      type="number"
      name="weapons"
    > Waffen <br/>
    <input
      v-model.number="shields"
      type="number"
      name="shields"
    > Schilder <br/>
    <input
      v-model.number="coffee"
      type="number"
      name="coffee"
    > Kaffee <br/>
    <input
      v-model.number="ac"
      type="number"
      name="ac"
    > Klimaanlage <br/>
    <input
      v-model.number="mousedroids"
      type="number"
      name="mousedroids"
    > Maus-Droids <br/>
  </p>

  <p>
    Aktueller Gesamtbetrag: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Abschicken"
    >
  </p>

</form>
```

Beachte die Ansammlung der Eingabefelder, die fünf unterschiedliche Funktionalitäten abdecken. Beachte auch, dass `.number` zum Attribut `v-model` hinzugefügt wurde. Das sagt Vue, dass der Wert zu einer Zahl umgewandet wird, wenn Du es verwendest. Jedoch gibt es einen Fehler in dieser Funktionalität: Wenn kein Wert eingegeben ist, wird er zurück in ein String umgewandelt. Du kannst unten den Workaround sehen. Um es für den Benutzer ein wenig leichter zu machen, haben wir auch den aktuellen Gesamtbetrag hinzugefügt, damit der Benutzer in Echtzeit sehen kann, wie hoch der Gesamtbetrag ist. Sehen wir uns jetzt den JavaScript-Code an.

``` js
const app = new Vue({
  el: '#app',
  data:{
    errors: [],
    weapons: 0,
    shields: 0,
    coffee: 0,
    ac: 0,
    mousedroids: 0
  },
  computed: {
     total: function () {
	   // Wir müssen die Umwandlung machen, weil Vue leere
	   // Werte in Texte umwandelt
       return Number(this.weapons) +
         Number(this.shields) +
         Number(this.coffee) +
         Number(this.ac+this.mousedroids);
     }
  },
  methods:{
    checkForm: function (e) {
      this.errors = [];

      if (this.total != 100) {
        this.errors.push('Der Gesamtbetrag muss 100 sein!');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    }
  }
})
```

Wir haben den Gesamtbetrag als einen berechneten Wert aufgesetzt. Abgesehen vom oben erwähnten Fehler war es ziemlich einfach. Meine `checkForm`-Methode braucht jetzt nur zu prüfen, ob der Gesamtbetrag 100 ist und das ist es. Du kannst mit diesem Beispiel hier spielen:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqGoy" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 3" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqGoy/">form validation 3</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Serverseitige Validierung

In meinem letzten Beispiel haben wir etwas gebaut, das Ajax verwendet, um die Validierung am Server vorzunehmen. Das Formular wird nach einem neuen Produkt fragen und dann prüfen, dass der Name einzigartig ist. Wir haben schnell eine serverlose [OpenWhisk](http://openwhisk.apache.org/)-Aktion geschrieben, um die Validierung umzusetzen. Obwohl sie nicht wirklich wichtig ist, hier ist die Logik:

``` js
function main(args) {
    return new Promise((resolve, reject) => {
        // Schlechte Produktnamen: vista, empire, mbp
        const badNames = ['vista', 'empire', 'mbp'];

        if (badNames.includes(args.name)) {
          reject({error: 'Das Produkt existiert bereits'});
        }

        resolve({status: 'ok'});
    });
}
```

Grundsätzlich sind alle Namen ausser "vista", "empire", und "mbp" akzeptabel. OK, sehen wir uns das Formular an.

``` html
<form
  id="app"
  @submit="checkForm"
  method="post"
>

  <p v-if="errors.length">
    <b>Korrigiere bitte den/die folgenden Fehler:</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Neuer Produktname: </label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <input
      type="submit"
      value="Abschicken"
    >
  </p>

</form>
```

Es gibt hier nichts Besonderes. Daher gehen wir weiter zum JavaScript.

``` js
const apiUrl = 'https://openwhisk.ng.bluemix.net/api/v1/web/rcamden%40us.ibm.com_My%20Space/safeToDelete/productName.json?name=';

const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: ''
  },
  methods:{
    checkForm: function (e) {
      e.preventDefault();

      this.errors = [];

      if (this.name === '') {
        this.errors.push('Produktname wird benötigt.');
      } else {
        fetch(apiUrl + encodeURIComponent(this.name))
        .then(res => res.json())
        .then(res => {
          if (res.error) {
            this.errors.push(res.error);
          } else {
		    // Leite zu einer neuen URL um, or mache etwas im
			// Erfolgsfall
            alert('ok!');
          }
        });
      }
    }
  }
})
```

Wir starten mit einer Variable, die die URL der auf OpenWhisk laufenden API darstellt. Schaue jetzt `checkForm` an. In dieser Version verhindern wir immer, dass das Formular abgeschickt wird (das Gleiche könnten wir übrigens in HTML ohne Vue auch machen). Du kannst die einfache Überprüfung sehen, ob `this.name` leer ist. Dann wird die API verwendet. Wenn die Überprüfung zum negativen Ergebnis führt, fügen wir einen Fehler hinzu wie vorher. Wenn die Validierung keinen Fehler meldet, tun wir im Augenblick nichts (ausser einem alert). Du könntest jedoch den Benutzer zu einer neuen Seite mit dem eingegebenen Produktnamen in der URL weiterleiten, oder andere Dinge tun. Du kannst dieses Demo hier ausführen:

<p data-height="265" data-theme-id="0" data-slug-hash="BmgzeM" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 4" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/BmgzeM/">form validation 4</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Alternative Entwurfsmuster

Dieses Kochrezept konzentriert sich auf manuell programmierte Validierung. Es gibt aber auch einige sehr gute Vue-Bibliotheken, die Dir eine Menge Arbeit abnehmen werden. Die Umschaltung zu einer vorverpackten Bibliothek kann die endgültige Größe Deiner Anwendung beeinflussen, aber die Vorteile können enorm sein. Du wirst einen Code haben, der (höchstwahrscheinlich) sehr genau getestet ist und auch regelmäßig aktualisiert wird. Beispiele von Bibliotheken für Formularvalidierung sind:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](http://vee-validate.logaretm.com/)
