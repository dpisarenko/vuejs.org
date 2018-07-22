---
title: API
type: api
---

## Globale Konfiguration

`Vue.config` ist ein Objekt, welches die globake Konfiguration von Vue enthält. Sie können die untenstehenden Eigenschaften dieses Objekts verändern, before Sie Ihre Anwendung zum Start vorbereiten (bootstrappen):

### silent

- **Datentyp:** `boolean`

- **Default-Wert:** `false`

- **Verwendung:**

  ``` js
  Vue.config.silent = true
  ```

  Unterdrücke alle Log- und Warnmeldungen von Vue.

### optionMergeStrategies

- **Datentyp:** `{ [key: string]: Function }`

- **Default-Wert:** `{}`

- **Verwendung:**

  ``` js
  Vue.config.optionMergeStrategies._my_option = function (parent, child, vm) {
    return child + 1
  }

  const Profile = Vue.extend({
    _my_option: 1
  })

  // Profile.options._my_option = 2
  ```

  Erstelle benutzerdefinierte Strategien für das Zusammenführen der Optionen.
  
  Die Zusammenführungsstrategie bekommt zwei Werte als Parameter. Der erste Parameter (`parent`) ist die Option, die auf der Instanz der höheren Ebene (Elterninstanz) definiert ist. Der zweite (`child`) -- jene Option, die auf der Kindinstanz definiert ist. Die Vue-Instanz mit dem Kontext (`vm`) wird als der dritte Parameter übergeben. 

- **Siehe auch:** [Benutzerdefinierte Zusammenführungsstrategien](../guide/mixins.html#Custom-Option-Merge-Strategies)

### devtools

- **Datentyp:** `boolean`

- **Default-Wert:** `true` (`false` in Builds für die Produktionsumgebung)

- **Verwendung:**

  ``` js
  // Stellen Sie sicher, dass diese Einstellung synchron, unmittelbar nach dem Laden von Vue vorgenommen wird
  Vue.config.devtools = true
  ```
  
  Konfiguriert, ob die Untersuchung mit [vue-devtools](https://github.com/vuejs/vue-devtools) erlaubt ist. Der Default-Wert dieser Einstellung ist `true` in Builds für die Entwicklungsumgebung und `false` für die Produktionsumgebung. Sie können sie auf `true` setzen, um die Untersuchung in Produktions-Builds zu erlauben. 

### errorHandler

- **Datentyp:** `Function`

- **Default-Wert:** `undefined`

- **Verwendung:**

  ``` js
  Vue.config.errorHandler = function (err, vm, info) {
    // Fehlerbehandlung
    // `info` ist eine Vue-spezifische Fehlerinformation, z. B. in
    // welcher Lebenszyklus-Phase der Fehler gefunden wurde.
    // Verfügbar ab Version 2.2.0. 
  }
  ```
  
  Weise eine Fehlerbehandlungsroutine für nicht abgefangene Fehler, die während der Ausführung der render-Funktion der Komponenten sowie in watchern auftreten. Die Fehlerbehandlungsroutine wird mit dem Fehler und der jeweiligen Vue-Instanz aufgerufen.

  > In 2.2.0+ fängt diese Routine aucu Fehler in den Lebenzyklus-Phasen der Komponenten ab. Außerdem: Wenn dieser Hook gleich `undefined` ist, werden die abgefangenen Fehler mit `console.error` geloggt, anstatt die Anwendung abstürzen zu lassen.

  > In 2.4.0+ fängt dieser Hook auch jene Fehler ab, die in benutzerdefinierten Ereignishandlern von Vue geworfen wurden.
 
  > Services zur Fehlerverfolgung wie [Sentry](https://sentry.io/for/vue/) und [Bugsnag](https://docs.bugsnag.com/platforms/browsers/vue/) stellen offizielle Integrationen mittels dieser Option zur Verfügung.

### warnHandler

> Neu in 2.4.0+

- **Datentyp:** `Function`

- **Default-Wert:** `undefined`

- **Verwendung:**

  ``` js
  Vue.config.warnHandler = function (msg, vm, trace) {
    // `trace` is das Trace der Komponentenhierarchie
  }
  ```
  
  Weist eine benutzerdefinierte Routine für Runtime-Vue-Warnungen zu. Beachten Sie, dass dies nur in der Entwicklungsumgebung funktioniert und die Einstellung in Produktion ignoriert wird.

### ignoredElements

- **Datentyp:** `Array<string | RegExp>`

- **Default-Wert:** `[]`

- **Verwendung:**

  ``` js
  Vue.config.ignoredElements = [
    'my-custom-web-component',
    'another-web-component',
    // Verwenden Sie `RegExp`, um alle Elemente zu ignorieren, die mit "ion-" beginnen
    // Nur ab 2.5
    /^ion-/
  ]
  ```
  
  Bewirkt, dass Vue benutzerdefinierte Element, die außerhalb von Vue definiert waren (z. B. unter Verwendung der APIs von Web Components), ignoriert. Andernfalls wird es eine Warnung bezüglich eines `Unbekannten benutzerdefinierten Elements` (`Unknown custom element`) werfen und davon ausgehen, dass Sie vergessen haben, eine globale Komponente zu registrieren oder einen Tippfehler im Namen der Komponente gemacht haben.

### keyCodes

- **Datentyp:** `{ [key: string]: number | Array<number> }`

- **Default-Wert:** `{}`

- **Verwendung:**

  ``` js
  Vue.config.keyCodes = {
    v: 86,
    f1: 112,
    // camelCase wird hier nicht funktionieren
    mediaPlayPause: 179,
    // Stattdessen können Sie kebab-case mit doppelten Hochkommas verwenden
    "media-play-pause": 179,
    up: [38, 87]
  }
  ```

  ```html
  <input type="text" @keyup.media-play-pause="method">
  ```

  Definiert einen Benutzerdefinierten Alias für `v-on`.

### performance

> Neu in 2.2.0+

- **Datentyp:** `boolean`

- **Default-Wert:** `false (from 2.2.3+)`

- **Verwendung**:

  Setzen Sie diese Einstellung auf `true`, damit Sie die Performance der Komponenteninitialisierung, -kompilierung, -rendering, sowie -patches in devtools im Browser (im Abschnitt "Performance/Timeline") verfolgen können.  Funktioniert nur im Entwicklermodus und nur in Browsern, die [performance.mode](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API unterstützen. 

### productionTip

> Neu in 2.2.0+

- **Datentyp:** `boolean`

- **Default-Wert:** `true`

- **Verwendung**:
  
  Setzen Sie diesen Wert auf `false`, um zu verhindern, dass beim Start eine Warnmeldung über die Arbeit im Entwicklungsmodus erscheint.

## Globale API

### Vue.extend( options )

- **Argumente:**
  - `{Object} options`

- **Verwendung:**
  
  Erstellt eine "Unterklasse" des Vue-Basiskontruktors. Das Argument sollte ein Objekt sein, das die Komponenten-Optionen enthält.
  
  Es gibt einen Sonderfall mit der `data`-Option - es muß eine Funktion sein, wenn es mit `Vue.extend()` verwendet wird.

  ``` html
  <div id="mount-point"></div>
  ```

  ``` js
  // Erstelle einen Konstruktor
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
    data: function () {
      return {
        firstName: 'Walter',
        lastName: 'White',
        alias: 'Heisenberg'
      }
    }
  })
  // Erstelle eine Instanz der Klasse Profile und montiere sie auf ein Element.
  new Profile().$mount('#mount-point')
  ```
  
  wird Folgendes bewirken:

  ``` html
  <p>Walter White aka Heisenberg</p>
  ```

- **Siehe auch:** [Komponenten](../guide/components.html)

### Vue.nextTick( [callback, context] )

- **Argumente:**
  - `{Function} [callback]`
  - `{Object} [context]`

- **Verwendung:**
  
  Bewirkt, dass das Callback nach dem nächsten DOM-Updatezyklus ausgeführt wird. verwenden Sie es unmittelbar nachdem Sie Daten verändert haben, um auf den nächsten DOM-Update zu warten. 

  ``` js
  // Daten verändern
  vm.msg = 'Hello'
  // DOM ist noch nicht aktualisiert
  Vue.nextTick(function () {
    // Jetzt ist das DOM aktualisiert
  })

  // Verwendung als Promise (ab 2.1.0, siehe Anmerkung unten)
  Vue.nextTick()
    .then(function () {
      // DOM aktualisiert 
    })
  ```
  
  > Neu in 2.1.0+: Gibt einen Promise zurück, wenn kein Callback übergeben wurde und Promise in der Ausführungsumgebung unterstützt wird. Bitte beachten Sie, dass Promise polyfill nicht mit Vue mitgeliefert wird, daher werden Sie ein polyfill selber zur Verfügung stellen müssen, wenn Sie Browser ansprechen, die keine native Unterstützung für Promises haben (IE, ich sehe Dir in die Augen).

- **Siehe auch:** [Asychrone Aktualisierungswarteschlange](../guide/reactivity.html#Async-Update-Queue)

### Vue.set( target, key, value )

- **Argumente:**
  - `{Object | Array} target`
  - `{string | number} key`
  - `{any} value`

- **Rückgabewert:** Der Wert, auf den die Eigenschaft gesetzt wurde.

- **Verwendung:**

  Fügt eine Eigenschaft zu einem reaktiven Objekt hinzu und stellt sicher, dass die Eigenschaft auch reaktiv ist, und daher View-Updates auslöst. Man muß diese Methode verwenden, um neue Eigenschaften zu reaktiven Objekten hinzuzufügen, da Vue das Hinzufügen von normalen Eigenschaften nicht erkennt (e.g. `this.myObject.newProperty = 'hi'`).

  <p class="tip">Das Zielobjekt (`target`) darf weder eine Vue-Instanz, noch das Wurzeldatenobjekt einer Vue-Instanz sein.</p>

- **Siehe auch:** [Detaillierte Beschreibung der Reaktivität](../guide/reactivity.html)

### Vue.delete( target, key )

- **Argumente:**
  - `{Object | Array} target`
  - `{string | number} key/index`

  > Nur in 2.2.0+: Funktioniert auch mit Array + index.

- **Verwendung:**
  
  Löscht eine Eigenschaft eines Objekts. Wenn das Objekt reaktiv ist, stellt sicher, dass die Löschung View-Updates auslöst. Vue kann keine Löschungen der Eigenschaften erkennen und diese Methode erlaubt es, diese Einschränkung zu umgehen. Allerdings werden Sie diese Methode selten brauchen. 

  <p class="tip">Das Zielobjekt (`target`) darf weder eine Vue-Instanz, noch das Wurzeldatenobjekt einer Vue-Instanz sein.</p>

- **Siehe auch:** [Detaillierte Beschreibung der Reaktivität](../guide/reactivity.html)

### Vue.directive( id, [definition] )

- **Argumente:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Verwendung:**
  
  Registriert eine globale Direktive, oder ruft sie ab.

  ``` js
  // Registrieren
  Vue.directive('my-directive', {
    bind: function () {},
    inserted: function () {},
    update: function () {},
    componentUpdated: function () {},
    unbind: function () {}
  })

  // Registrieren (Funktionsdirektive)
  Vue.directive('my-directive', function () {
    // Dieser Teil wird als `bind` und `update` aufgerufen
  })

  // Getter, gebe die Direktive zurück, falls diese registriert ist
  var myDirective = Vue.directive('my-directive')
  ```

- **Siehe auch:** [Benutzerdefinierte Direktiven](../guide/custom-directive.html)

### Vue.filter( id, [definition] )

- **Argumente:**
  - `{string} id`
  - `{Function} [definition]`

- **Verwendung:**

  Registriert einen globalen Filter, oder ruft diesen ab.

  ``` js
  // Registrieren
  Vue.filter('my-filter', function (value) {
    // Bearbeiteten Wert zurückgeben
  })
  
  // Getter, gebe den Filter zurück, falls dieser registriert ist.
  var myFilter = Vue.filter('my-filter')
  ```

- **Siehe auch:** [Filter](../guide/filters.html)

### Vue.component( id, [definition] )

- **Argumente:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Verwendung:**

  Registriert eine globale Komponente, oder ruft eine solche ab. Bei der Registrierung wird der `name` der Komponente automatisch auf die übergeben `id` gesetzt.

  ``` js
  // Registriert einen erweiterten Konstruktor
  Vue.component('my-component', Vue.extend({ /* ... */ }))
  
  // Registriert einen Optionsobjekt (Vue.extend wird automatisch aufgerufen)
  Vue.component('my-component', { /* ... */ })
  
  // Rufe eine registrierte Komponente ab (es wird immer der Konstruktor zurückgegeben)
  var MyComponent = Vue.component('my-component')
  ```

- **Siehe auch:** [Komponenten](../guide/components.html)

### Vue.use( plugin )

- **Argumente:**
  - `{Object | Function} plugin`

- **Verwendung:**

  Installiert ein Vue.js-Plugin. Wenn das Plugin ein Object ist, muss es eine öffentliche `install`-Methode haben. Wenn es eine Funktion ist, wird sie als die Installationsmethode behandelt. Wenn die Installationsmethode aufgerufen wird, wird ihr Vue als Argument übergeben.
  
  Wird diese Methode mehrmals mit dem gleichen Plugin aufgerufen, dann wird das Plugin nur ein Mal installiert.

- **Siehe auch:** [Plugins](../guide/plugins.html)

### Vue.mixin( mixin )

- **Argumente:**
  - `{Object} mixin`

- **Verwendung:**
  
  Wende ein Mixin global an, sodass alle Vue-Instanzen, die nach diesem Aufruf erstellt wurden, davon betroffen sind. Dies kann von Plugin-Autoren verwendet werden, um benutzerdefiniertes Verhalten in Komponenten zu injiziern. **Im Anwendungscode nicht empfohlen**.

- **Siehe auch:** [Globales Mixin](../guide/mixins.html#Global-Mixin)

### Vue.compile( template )

- **Argumente:**
  - `{string} template`

- **Verwendung:**

  Kompiliert einen Vorlagetext (template string) in eine render-Funktion. **Verfügbar nur im vollständigen Build.** 

  ``` js
  var res = Vue.compile('<div><span>{{ msg }}</span></div>')

  new Vue({
    data: {
      msg: 'hello'
    },
    render: res.render,
    staticRenderFns: res.staticRenderFns
  })
  ```

- **Siehe auch:** [Render-Funktionen](../guide/render-function.html)

### Vue.version

- **Details**: Gibt die installierte Version von Vue als String zurück. Dies ist besonders hilfreich für Community-Plugins und -Komponenten, bei denen je nach Vue-Version unterschiedliche Strategien verwendet werden können.

- **Verwendung**:

  ```js
  var version = Number(Vue.version.split('.')[0])

  if (version === 2) {
    // Vue v2.x.x
  } else if (version === 1) {
    // Vue v1.x.x
  } else {
    // Nicht unterstützte Version von Vue
  }
  ```

## Optionen / Daten

### data

- **Datentyp:** `Object | Function`

- **Einschränkung:** `Function`s werden nur in Komponentendefinitionen unterstützt.

- **Details:**

  Das Daten-Objekt für die Vue-Instanz. Vue wird dessen Eigenschaften rekursiv in getter/setter-Paare umwandeln, um es "reaktiv" zu machen ("beobachten"). **Das Objekt muss einfach sein**: native Objekte wie Browser-API-Objekte und Prototyp-Eigenschaften werden ignoriert. Die Faustregel lautet: Daten sollen nur Daten sein. Daher wird es nicht empfohlen, Objekte mit ihrem eigenen, zustandsabhängigen Verhalten, zu beobachten.
  
  Sobald die Eigenschaften beobachtet wurde, ist es nicht mehr möglich, reaktive Eigenschaften zum Wurzeldatenobjekt hinzuzufügen. Daher wird empfohlen, alle reaktiven Eigenschaften auf Wurzelebene im Voraus, vor der Erstellung der Instanz, zu deklarieren.

  Nachdem die Instanz erstellt wurde, kann man auf das usprüngliche Datenobjekt mit `vm.$data` zugreifen. Die Vue-Instanz hat Stellvertreter für alle Eigenschaften, die im Daten-Objekt gefunden werden, sodass `vm.a` gleichbedeutend mit `vm.$data.a` ist. 

  Es werden **keine** Stellvertreter für Eigenschaften, deren Namen mit `_` oder `$` beginnen, erstellt, weil sie mit den internen Eigenschaften und API-Methoden von Vue kollidieren können. Sie werden auf sie über `vm.$data._property` zugreifen müssen.

  Wenn Sie eine **Komponente** definieren, muss `data` als eine Funktion deklariert werden, welche das ursprüngliche Datenobjekt zurückgibt, da es mehrere Instanzen geben wird, die mit der gleichen Definition erstellt wurden. Wenn wir ein einfaches Objekt für `data` verwenden, wird dieses Objekt **über Referenz** von allen erstellten Instanzen **geteilt** werden! Indem wir eine `data`-Funktion zur Verfügung stellen, können wir sie verwenden, um eine Kopie der ursprünglichen Daten für jede Instanz der Komponenten zu bekommen.

  Wenn nötig, kann man einen "tiefen Klon" (deep clone) des ursprünglichen Objekts erhalten, indem man `vm.$data` an `JSON.parse(JSON.stringify(...))` übergibt.

- **Beispiel:**

  ``` js
  var data = { a: 1 }

  // Direkte Erstellung einer Instanz
  var vm = new Vue({
    data: data
  })
  vm.a // => 1
  vm.$data === data // => true

  // Eine Funktion muss verwendet werden, wenn wir Vue.extend(...) einsetzen.
  // must use function when in Vue.extend()
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  ```
  
  Beachten Sie, dass Sie wenn Sie die Pfeilnotation mit der `data`-Eigenschaft verwenden, `this` nicht die Instanz der Komponente sein wird. Jedoch können Sie immer noch auf die Instanz als das erste Argument der Funktion zugreifen:

  ```js
  data: vm => ({ a: vm.myProp })
  ```

- **Siehe auch:** [Detaillierte Beschreibung der Reaktivität](../guide/reactivity.html)

### props

- **Datentyp:** `Array<string> | Object`

- **Details:**

  Eine Liste/Hash der Attribute, die öffentlich sind, um Daten von der Elternkomponente zu empfangen. Die Eigenschaft hat eine einfache, Array-basierte Syntax, sowie eine alternative, Objekt-basierte Syntax, welche fortgeschrittene Einstellungen wie Typenüberprüfung, benutzerdefinierte Validation, und Default-Werte, erlaubt.

- **Beispiel:**

  ``` js
  // Einfache Syntax
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })
  
  // Objekt-Syntax mit Validierung
  Vue.component('props-demo-advanced', {
    props: {
      // Typenüberprüfung
      height: Number,
      // Überprüfung des Typs plus andere Validierungen
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: function (value) {
          return value >= 0
        }
      }
    }
  })
  ```

- **Siehe auch:** [Eigenschaften](../guide/components.html#Props)

### propsData

- **Datentyp:** `{ [key: string]: any }`

- **Einschränkung:** Wird nur bei der Erstellung einer Instanz mit `new` berücksichtigt.

- **Details:**

  Übergebt Eigenschaften an eine Instanz während deren Erstellung. Dies ist vor allem nützlich, um das Unit-Testing einfacher zu machen.

- **Beispiel:**

  ``` js
  var Comp = Vue.extend({
    props: ['msg'],
    template: '<div>{{ msg }}</div>'
  })

  var vm = new Comp({
    propsData: {
      msg: 'hello'
    }
  })
  ```

### computed

- **Datentyp:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Details:**

  Berechnet Eigengschaften, welche in die Vue-Instanz "gemixt" (mixed into) werden. Bei allen Gettern und Settern wird der `this`-Kontext automatisch an die Vue-Instanz gebunden. 

  Beachten Sie, dass wenn Sie die Pfeilnotation mit einer berechneten Eigenschaft verwenden, `this` nicht die Komponenteninstanz sein wird. Jedoch können Sie auf die letztere als das erste Argument der Funktion zugreifen:

  ```js
  computed: {
    aDouble: vm => vm.a * 2
  }
  ```
  
  Berechnete Eigenschaften werden gecached, und nur dann neu berechnet, wenn sich die reaktive Abhängigkeit ändert. Beachten Sie bitte, dass eine berechnete Eigenschaft __nicht__ aktualisiert werden wird, wenn sich eine bestimmte Abhängigkeit außerhalb des Scope der Instanz befindet (d. h. nicht reaktiv ist).  

- **Beispiel:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // Nur get
      aDouble: function () {
        return this.a * 2
      },
      // Sowohl get, als auch set
      aPlus: {
        get: function () {
          return this.a + 1
        },
        set: function (v) {
          this.a = v - 1
        }
      }
    }
  })
  vm.aPlus   // => 2
  vm.aPlus = 3
  vm.a       // => 2
  vm.aDouble // => 4
  ```

- **Siehe auch:** [Berechnete Eigenschaften](../guide/computed.html)

### methods

- **Datentyp:** `{ [key: string]: Function }`

- **Details:**

  Methoden, die in die Vue-Instanz hineingemixt (mixed in) werden sollen. Sie können auf diese Methoden direkt über die VM-Instanz zugreifen, oder sie in Direktiv-Ausdrücken verwenden. Bei allen Methoden wird der `this`-Kontext automatisch an die Vue-Instanz gebunden sein.

  <p class="tip">Beachten Sie, dass Sie __keine Pfeilnotation verwenden sollen, um eine Methode zu definieren__ (z. B. `plus: () => this.a++`). Der Grund besteht darin, dass Pfeilfunktionen sich an den Eltern-Kontext binden. Daher wird `this` wider Erwarten nicht die Vue-Instanz und `this.a` wird undefiniert sein.</p>

- **Beispiel:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    methods: {
      plus: function () {
        this.a++
      }
    }
  })
  vm.plus()
  vm.a // 2
  ```

- **Siehe auch:** [Ereignisbehandlung](../guide/events.html)

### watch

- **Datentyp:** `{ [key: string]: string | Function | Object | Array}`

- **Details:**

  Eine Schlüssel-Wert-Abbildung, bei der die Schlüssel die zu beobachtenden Ausdrücke, und die werte die zugehörigen Callbacks sind. Der Wert kann auch ein String eines Methodennamen sein, oder ein Object, welches zusätzliche Optionen enthält. Die Vue-Instanz wird `$watch()` für jeden Eintrag in der Abbildung während der Instanziierung aufrufen.

- **Beispiel:**

  ``` js
  var vm = new Vue({
    data: {
      a: 1,
      b: 2,
      c: 3,
      d: 4,
      e: {
        f: {
          g: 5
        }
      }
    },
    watch: {
      a: function (val, oldVal) {
        console.log('new: %s, old: %s', val, oldVal)
      },
      // String mit dem Namen der Methode
      b: 'someMethod',
      // Tiefer Beobachter
      c: {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      },
      // Das Callback wird unmittelbar nach dem Start des Beobachtens aufgerufen
      d: {
        handler: function (val, oldVal) { /* ... */ },
        immediate: true
      },
      e: [
        function handle1 (val, oldVal) { /* ... */ },
        function handle2 (val, oldVal) { /* ... */ }
      ],
      // Beobachte den Wert von vm.e.f: {g: 5}
      'e.f': function (val, oldVal) { /* ... */ }
    }
  })
  vm.a = 2 // => new: 2, old: 1
  ```
  <p class="tip>">Beachten Sie, dass Sie __keine Pfeilfunktion verwenden dürfen, wenn Sie einen watcher definieren__ (z. B. `searchQuery: newValue => this.updateAutocomplete(newValue)`). Der Grund besteht darin, dass Pfeilfunktionen sich an an den Elternkontext anbinden, sodass `this` wider Erwarten nicht die Vue-Instanz und `this.updateAutocomplete` undefiniert sein wird.</p>

- **Siehe auch:** [Instanzenmethoden / Daten - vm.$watch](#vm-watch)

## Options / DOM

### el

- **Datentyp:** `string | HTMLElement`

- **Einschränkung:** Wird nur bei Erstellung einer Instanz mit `new` berücksichtigt.

- **Details:**

  Übergibt der Vue-Instanz ein existierendes DOM-Element, wo sie montiert werden soll. Es kann ein CSS-Selektorstring oder ein richties HTMLElement sein.

  Nachdem die Instanz montiert ist, wird das aufgelöste Element über `vm.$el` zugänglich sein.

  Falls diese Option während der Instanziierung verfügbar ist, wird die Instanz unmittelbar danach die Kompilerung beginnen. Andernfalls muss der Benutzer explizit `vm.$mount()` aufrufen, um die Kompilierung manuell zu starten.

  <p class="tip">Das übergebene Element dient lediglich als ein Montierungspunkt. Im Gegensatz zu Vue 1.x wird das montierte Element mit dem Vue-generierten DOM immer ersetzt werden. Es wird deswegen nicht empfohlen, die Wurzelinstanz von Vue auf `<html>` oder `<body>` zu montieren.</p> 

  <p class="tip>">Wenn weder die `render`-Funktion, noch die `template`-Option vorhanden ist, wird der in-DOM-HTML-Code des zu montierenden Elements als die Vorlage extrahiert. In diesem Fall sollten Runtime + Compiler build von Vue eingesetzt werden.</p>

- **Siehe auch:**
  - [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)
  - [Runtime und Compiler vs. Nur Runtime](../guide/installation.html#Runtime-Compiler-vs-Runtime-only)

### template

- **Datentyp:** `string`

- **Details:**

  Eine String-Vorlage, die als Markup für die Vue-Instanz verwendet werden soll. Die Vorlage wird das Element, auf das montiert wird, **ersetzen**. Jegliches existierende Markup innerhalb dieses Elements wird ignoriert werden, es sei denn es gibt Content-Verteilungsslots in der Vorlage.

  Wenn der Text mit `#` beginnt, wird es als querySelector verwendet und innerHTML des angewählten Elements als den Vorlagestring verwenden. Damit kann man den Trick mit `<script type="x-template">` verwenden, um Vorlagen zu inkludieren.

  <p class="tip">Sicherheitstechnisch sollten Sie nur jene Vue-Vorlagen verwenden, denen Sie vertrauen können. Verwenden Sie niemals benutzergenerierte Inhalte (UGC) als Ihre Vorlage.</p>
  
  <p class="tip">Wenn die render-Funktion in der Vue-Option vorhanden ist, wird die Vorlage ignoriert.</p>

- **Siehe auch:**
  - [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)
  - [Content-Verteilung mit Slots](../guide/components.html#Content-Distribution-with-Slots)

### render

  - **Datentyp:** `(createElement: () => VNode) => VNode`

  - **Details:**
    
    Eine Alternative zu String-Vorlagen, die es Ihnen erlaubt, von der vollen Programmierkraft von JavaScript Gebrauch zu machen. Die render-Funktion bekommt eine `createElement`-Methode als ihr erstes Argument, mit der `VNode`s erzeugt werden.

    Wenn die Komponent funktional ist, erhält die render-Funktion ein zusätzliches Argument `context`, welches Zugang zu kontext-sensitiven Daten gewährt (funktionale Komponenten sind instanzlos).

    <p class="tip">Die `render`-Funktion hat Vorrang vor der render-Funktion, die aus `template`-Option oder der in-DOM-HTML-Template des montierten Elements (spezifiziert durch die `el`-Option), kompiliert wurde.</p>

  - **Siehe auch:** [Render-Funktionen](../guide/render-function.html)

### renderError

> New in 2.2.0+

  - **Datentyp:** `(createElement: () => VNode, error: Error) => VNode`

  - **Details:**

    **Funktioniert nur im Entwicklungsmodus.**

    Erlaubt es, eine alternative Render-Ausgabe zu machen, falls die Default-Funktion `render` fehlschlägt. Der Fehler wird an `renderError` als zweites Argument übergeben. Dies ist in Verbindung mit hot-reload besonders nützlich. 

  - **Beispiel:**

    ``` js
    new Vue({
      render (h) {
        throw new Error('oops')
      },
      renderError (h, err) {
        return h('pre', { style: { color: 'red' }}, err.stack)
      }
    }).$mount('#app')
    ```

  - **Siehe auch:** [Render-Funktionen](../guide/render-function.html)

## Optionen / Lebenszyklushooks

<p class="tip">Bei allen Lebenszyklushooks wird der `this`-Kontext automatisch an die Instanz gebunden, sodass Sie auf Daten, berechnete Eigenschaften, und Methoden zugreifen können. Das bedeutet, dass Sie __die Pfeilfunktionen nicht verwenden dürfen, um eine Lebenszyklusmethode zu definieren__ (z. B. `created: () => this.fetchTools()`). Der Grund ist, dass Pfeilfunktionen sich an den Elternkontext binden, daher wird `this` wider Erwarten nicht die Vue-Instanz und `this.fetchTodos` wird undefiniert sein.</p>

### beforeCreate

- **Datentyp:** `Function`

- **Details:**

  Wird asynchron aufgerufen, unmittelbar nachdem die Instanz initialisiert wurde und before das Einrichten der Beobachtung von Daten, Watch-Event-Callbacks beginnt. 

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### created

- **Datentyp:** `Function`

- **Details:**

  Wird synchron aufgerufen, nachdem die Instanz erstellt ist. Zu diesem Zeitpunkt hat die Instanz die Bearbeitung der Optionen abgeschlossen und das bedeutet, dass Folgendes eingerichtet wurde: Datenbeobachtung, berechnete Eigenschaften, Methoden, Watch/Event-Callbacks. Jedoch had das Montieren noch nicht begonnen und die Eigenschaft `$el` ist noch nicht verfügbar. 

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### beforeMount

- **Datentyp:** `Function`

- **Details:**
  
  Wird aufgerufen, just before das Montieren beginnt: Die `render`-Funktion wird da zum ersten Mal auferufen werden.
  
  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### mounted

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen, nachdem die Instanz montiert wurde, wobei `el` durch das neu erstellte `vm.$el` ersetzt wird. Wenn die Wurzelinstanz an einem Element innerhalb eines Dokuments montiert wird, dann wird `vm.$el` ebenfalls innerhalb eines Dokuments liegen, wenn `mounted` aufgerufen wird.

  Beachten Sie, dass `mounted` **nicht** garantiert, dass alle Kind-Komponenten auch montiert wurden. Wenn Sie warten wollen, bis die gesamte Ansicht gerendert wurde, können Sie [vm.$nextTick](#vm-nextTick) in `mounted` verwenden:
  
  ``` js
  mounted: function () {
    this.$nextTick(function () {
      // Dieser Code wird ausgeführt, nachdem
      // die gesamte Ansicht gerendert wurde
    })
  }
  ```
  
  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### beforeUpdate

- **Datentyp:** `Function`

- **Details:**
  
  Wird aufgerufen, wenn sich die Daten ändern, bevor DOM gepatched wird. Dies ist ein guter Ort, um auf das existierende DOM vor dem Update zuzugreifen, z. B. um die manuell hinzugefügte Event-Listener zu entfernen.
  
  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen, weil nur der erste Render-Durchgang am Server stattfindet.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### updated

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen, nachdem eine Datenveränderung dazuführt, dass das virtuelle DOM neugerendert und gepatched wird.

  Zu diesem Zeitpunkt wird das DOM der Komponente aktualisiert sein, sodass Sie DOM-abhängige Operationen hier ausführen können. In den meisten Fällen sollten Sie jedoch keine Zustandsveränderungen in diesem Hook machen. Üblicherweise is eine [berechnete Eigenschaft](#computed) oder [watch](#watch) besser dafür geeignet, um auf Zustandsänderungen zu reagieren.
 
  Beachten Sie, dass `updated` **nicht** garantiert, dass alle Kindkomponenten auch neugerendert wurden. Wenn Sie darauf warten wollen, dass die gesamte Ansicht neugerendert wurde, können Sie [vm.$nextTick](#vm-nextTick) in `updated` verwenden: 

  ``` js
  updated: function () {
    this.$nextTick(function () {
      // Dieser Code wird ausgeführt, wenn
      // die gesamte Ansicht neugerendert 
      // wurde
    })
  }
  ```
  
  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### activated

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen, wenn ein keep-alive-Komponente aktiviert wird.

  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:**
  - [Eingebaute Komponenten - keep-alive](#keep-alive)
  - [Dynamische Komponenten - keep-alive](../guide/components.html#keep-alive)

### deactivated

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen, wenn eine keep-alive-Komponente deaktiviert wird.

  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:**
  - [Eingebaute Komponenten - keep-alive](#keep-alive)
  - [Dynamische Komponenten - keep-alive](../guide/components.html#keep-alive)

### beforeDestroy

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen just before die Vue-Instanz zerstört wird. Zu diesem Zeitpunkt ist die Instanz voll funktional.

  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### destroyed

- **Datentyp:** `Function`

- **Details:**

  Wird aufgerufen, nachdem eine Vue-Instanz zerstört wurde. Wenn dieser Hook aufgerufen wird, sind alle Direktiven dieser Vue-Instanz losgebunden, alle Event-Listener entfernt, und alle Kinder der Vue-Instanz zerstört.

  **Dieses Hook wird beim serverseitigen Rendering nicht aufgerufen.**

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

### errorCaptured

> Neu in 2.5.0+

- **Datentyp:** `(err: Error, vm: Component, info: string) => ?boolean`

- **Details:**
  
  Wird aufgerufen, wenn ein Fehler in einer der Kindkomponenten auftritt. Dieser Hook bekommt drei Argumente: den Fehler, die Instanz der Komponente, die den Fehler ausgelöst hat, und einen String mit Information, wo der Fehler aufgetreten ist. Der Hook kann `false` zurückgeben, damit der Fehler nicht weitergeleitet wird.

  <p class="tip">Sie können den Zustand der Komponente in diesem Hook verändern. Jedoch ist es wichtig, bedingte Elemente (conditionals) in Ihrer Vorlage bzw. eine render-Funktion, die die übrigen Inhalte im Falle eines Fehlers "kurzschließt", zu haben. Widrigenfalls wird die Komponenten in eine Endlos-Renderschleife geworfen.</p>

  **Regeln für die Weiterleitung der Fehler**

  - Per Voreinstellung werden alle Fehler immer noch an den globalen `config.errorHandler` geschickt, wenn dieser definiert ist. So können diese Fehler an analytische Dienste an einem Platz übermittelt werden.

  - Wenn es mehrere `errorCaptured` hooks in der Vererbungs- oder Elternkette einer Komponente gibt, werden sie alle mit dem selben Fehler aufgerufen.

  - Wenn `errorCaptured`-Hook selber einen Fehler wirft, werden sowohl dieser, als auch der ursprüngliche Fehler zum globalen `config.errorHandler` geschickt.

  - Ein `errorCaptured` hook kann `false` zurückgeben, um zu verhindern, dass der Fehler weitergeleitet wird. Im Grunde genommen bedeutet dies "dieser Fehler wurde bereits behandelt und sollte ignoriert werden." Dies wird verhindern, dass weitere `errorCaptured`-Hooks oder der globale `config.errorHandler` den Fehler bearbeiten.

## Optionen / Assets

### directives

- **Datentyp:** `Object`

- **Details:**

  Eine Abbildung von Direktiven, die der Vue-Instanz zur Verfügung stehen sollen.

- **Siehe auch:** [Benutzerdefinierte Direktiven](../guide/custom-directive.html)

### filters

- **Datentyp:** `Object`

- **Details:**

  Eine Abbildung von Filtern, die der Vue-Instanz zur Verfügung stehen sollen.

- **Siehe auch:** [`Vue.filter`](#Vue-filter)

### components

- **Datentyp:** `Object`

- **Details:**

  Eine Abbildung von Komponenten, die der Vue-Instanz zur Verfügung stehen sollen.

- **Siehe auch:** [Komponenten](../guide/components.html)

## Options / Composition

### parent

- **Datentyp:** `Vue instance`

- **Details:**

  Spezifiziert die Eltern-Instanz für die Instanz, die erstellt werden soll. Kreiert eine ElternKind-Beziehung zwischen den zweien. Das Kind kann auf das Eltern-Objekt kann über `this.$parent` zugreifen. Das Kind-Objekt wird an das Ende des `$children`-Arrays des Eltern-Objekts angehängt. 

  <p class="tip">Seien Sie sparsam bei der Verwendung von `$parent` und `$children` - sie dienen als Notausstieg. Sie sollten den Eigenschaften und Eregnissen bei der Eltern-Kind-Kommunikation Vorzug geben.</p>

### mixins

- **Datentyp:** `Array<Object>`

- **Details:**

  Die `mixins`-Option akzeptiert ein Array von Mixin-Objekten. Diese Mixin-Objekte können Instanzenoptionen wir normale Objektistanzen beinhalten. Sie werden mit den endgültigen Optionen nach gleicher Logik wie bei `Vue.extend()` zusammengeführt. Z. B. wenn Ihr Mixin ein erstelltes Hook enthält und die Komponente ebenfalls ein solches Hook hat, werden beide Hooks aufgerufen.

  Mixin-Hooks werden in jener Reihenfolge ausgeführt, in der sie zur Verfügung gestellt werden. Sie werden vor Hooks der Komponente aufgerufen.

- **Beispiel:**

  ``` js
  var mixin = {
    created: function () { console.log(1) }
  }
  var vm = new Vue({
    created: function () { console.log(2) },
    mixins: [mixin]
  })
  // => 1
  // => 2
  ```

- **Siehe auch:** [Mixins](../guide/mixins.html)

### extends

- **Datentyp:** `Object | Function`

- **Details:**

  Erlaubt es, eine Komponente von einer anderen (diese könnte entweder ein einfaches Objekt oder ein Konstruktor sein) deklarativ abzuleiten, ohne `Vue.extend` zu verwenden. Dies dient primär dazu, um das Erweitern von Komponenten, die in einer Datei definiert sind, zu vereinfachen.

  Dies ist ähnlich zu `mixins`.

- **Beispiel:**

  ``` js
  var CompA = { ... }

  // Erweitere CompA, ohne `Vue.extend` aufrufen zu müssen
  var CompB = {
    extends: CompA,
    ...
  }
  ```

### provide / inject

> Neu in 2.2.0+

- **Datentyp:**
  - **provide:** `Object | () => Object`
  - **inject:** `Array<string> | { [key: string]: string | Symbol | Object }`

- **Details:**

  <p class="tip">`provide` und `inject` werden primär für die Entwicklung von Plugins und Komponentenbibliotheken verwendet. Es wird NICHT empfohlen, sie im normalen Anwendungscode zu verwenden.</p>
  
  Dieses Optionen-Parr wird gemeinsam verwendet, damit eine Vorfahren-Komponente als Dependency Injector für alle ihre Kinder verwendet werden kann, unabhängig davon, wie tief die Hiearchie ist (solange sie sich in der selben Elternkette befinden). Wenn Sie mit React vertraut sind, ist dies sehr ähnlich zur Kontext-Funktionalität in React.
  
  Die `provide`-Option sollte ein Objekt oder eine Funktion sein, die ein Objekt zurückgibt. Dieses Objekt enthält Eigenschaften, die in die Nachfahren injiziert werden können. Sie können ES2015-Symbole as Schlüssel in diesem Objekt verwenden, aber nur in Umgebungen, die nativ `Symbol` und `Reflect.ownKeys` unterstützten.

  Die `inject`-Option sollte eines von zwei Dingen sein: 
  - Ein String-Array
  - Ein Objekt, bei dem die Schlüssel der lokale Bindungsname ist und der Wert
    - entweder Schlüssel (string oder Symbol), über den in verfügbaren Injizierungen gesucht werden kann, oder
    - ein Objekt, bei dem
      - die `from`-Eigenschaft der Schlüssel (string oder Symbol) über den in verfügbaren Injizierungen gesucht werden kann, und
      - die `default`-Eigenschaft für den Fall, dass die Suche fehlschlägt. 

  > Beachten Sie: Die `provide`- und `inject`-Bindungen sind NICHT reaktiv. Das ist Absicht. Wenn sie aber ein beobachtetes Objekt übergeben, bleiben die Eigenschaften dieses Objekts reaktiv.

- **Beispiel:**

  ``` js
  // Die Eltern-Komponente stellt 'foo' zur Verfügung.
  var Provider = {
    provide: {
      foo: 'bar'
    },
    // ...
  }

  // Kindkomponente, in die 'foo' injiziert wird.
  var Child = {
    inject: ['foo'],
    created () {
      console.log(this.foo) // => "bar"
    }
    // ...
  }
  ```
  
  Das Gleiche mit ES2015-Symbolen, einer `provide`-Funktion und einem `inject`-Objekt: 

  ``` js
  const s = Symbol()

  const Provider = {
    provide () {
      return {
        [s]: 'foo'
      }
    }
  }

  const Child = {
    inject: { s },
    // ...
  }
  ```
  
  > Die folgenden 2 Beispiele funktionieren mit Vue 2.2.1+. Vor dieser Version wurden die injizierten Werte nach Initialisierung von `props` und `data` aufgelöst.

  Verwendung eines injizierten Werts als Default-Wert für eine Eigenschaft:

  ```js
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default () {
          return this.foo
        }
      }
    }
  }
  ```

  Verwendung eines injizierten Werts als Dateneintrag:

  ```js
  const Child = {
    inject: ['foo'],
    data () {
      return {
        bar: this.foo
      }
    }
  }
  ```

  > In 2.5.0+ können Injektional optional sein und Default-Werte haben:

  ``` js
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  Wenn den Wert einer anderen Eigenschaft mit einem anderen Namen injizieren wollen, verwenden Sie `from`, um die Quell-Eigenschaft anzugeben:

  ``` js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  Ähnlich zu Default-Werten von Eigenschaften, werden Sie für nicht-primitive Werte eine Factory-Funktion brauchen.

  ``` js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

## Optionen / Sonstiges 

### name

- **Datentyp:** `string`

- **Einschränkung:** Wird nur als Komponentenoption berücksichtigt.

- **Details:**

  Erlaubt der Komponente, sich selbst in ihrer Vorlage rekursiv aufzurufen. Beachten Sie, dass wenn eine Komponente global mit `Vue.component()` registriert ist, die globale ID automatisch als ihr Name verwendet wird.

  Ein anderer Vorteil der Angabe einer `name`-Option ist das Debuggen. Benannte Komponenten führen zu aussagekräftigeren Warnmeldungen. Außerdem, wenn Sie eine Anwendung in [vue-devtools](https://github.com/vuejs/vue-devtools) untersuchen, werden unbenannte Komponenten als `<AnonymousComponent>` aufscheinen, was nicht besonders informativ ist. Indem Sie die `name`-Option angeben, bekommen Sie einen viel informativeren Komponentenbaum. 

### delimiters

- **Datentyp:** `Array<string>`

- **Default-Wert:** `{% raw %}["{{", "}}"]{% endraw %}`

- **Einschränkungen:** Diese Option ist nur im vollständigen Build, mit in-Browser-Kompilierung verfügbar.

- **Details:**

  Verändert die Trennzeichen bei der Interpolation des einfachen Textes.

- **Beispiel:**

  ``` js
  new Vue({
    delimiters: ['${', '}']
  })

  // Trennzeichen wurden zum ES6-Stringvorlatenstil geändert
  ```

### functional

- **Datentyp:** `boolean`

- **Details:**

  Bewirkt, dass eine Komponente zustands- (kein `data`) und instanzenlos (kein `this`-Kontext) wird. Es gibt nur eine `render`-Funktion, die virtuele Knoten zurückgibt und dadurch das Rendering wesentlich billiger macht.

- **Siehe auch:** [Funktionale Komponenten](../guide/render-function.html#Functional-Components)

### model

> Neu in 2.2.0

- **Datentyp:** `{ prop?: string, event?: string }`

- **Details:**

  Erlaubt einer benutzerdefinierten Komponente, jene Eigenschaften und Ereignisse anzupassen, die, die verwendet werden, wenn die Komponente mit `v-model` verwendet wird. Per Voreinstellung, verwendet `v-model` einer Komponente `value` als die Eigenschaft und `input` als das Ereignis. Jedoch kann es vorkommen, dass bestimmte Steuerelemente (z. B. eine Checkboxes und Radiobuttons) die `value`-Eigenschaft für andere Zwecke verwenden wollen. Der Einsatz der `model`-Option in diesem Fall kann einen Konflikt vermeiden.

- **Beispiel:**

  ``` js
  Vue.component('my-checkbox', {
    model: {
      prop: 'checked',
      event: 'change'
    },
    props: {
      // Dies erlabut es, die `value`-Eigenschaft für einen anderen Zweck zu verwenden.
      value: String,
      // Verwende `checked` als die Eigenschaft statt `value`
      checked: {
        type: Number,
        default: 0
      }
    },
    // ...
  })
  ```

  ``` html
  <my-checkbox v-model="foo" value="some value"></my-checkbox>
  ```

  Der obige Code ist äquivalent zu:

  ``` html
  <my-checkbox
    :checked="foo"
    @change="val => { foo = val }"
    value="some value">
  </my-checkbox>
  ```

### inheritAttrs

> Neu in 2.4.0+

- **Datentyp:** `boolean`

- **Default-Wert:** `true`

- **Details:**

  Per Voreinstellung werden Attribut-Bindungen aus dem Eltern-Scope, die als Eigenschaften nicht erkannt wurden, "durchfallen" und auf das Wurzelelement der Kind-Komponente als normale HTML-Attribute angewandt. Wenn Sie eine Komponente entwickeln, die das Zielelement oder eine andere Komponente umhüllt, wird dieses Verhalten nicht immer erwünscht sein. Sie können das Verhalten ausschalten, indem Sie `inheritAttrs` auf `false` setzen. Die Attribute sind über die Instanzeneigeschaft `$attr` verfügbar (auch neu in 2.4) und können mit `v-bind` explizit an ein nicht-Wurzelelement gebunden werden. 

  Anmerkung: Diese Option betrifft nicht die `class`- und `style`-Bindungen.

### comments

> Neu in 2.4.0+

- **Datentyp:** `boolean`

- **Default-Wert:** `false`

- **Einschränkungen:** Diese Option ist nur im vollständigen Build, mit in-Browser-Kompilierung verfügbar.

- **Details:**

  Wenn der Wert `true` ist, werden HTML-Kommentare, die in Vorlagen gefunden werden, beibehalten und gerendert. Per Voreinstellung werden sie verworfen.

## Eigenschaften der Instanzen

### vm.$data

- **Datentyp:** `Object`

- **Details:**

  Das Datenobjekt, das die Vue-Instanz beobachtet. Die Vue-Instanz gewährt Zugriff auf die Eigenschaften ihres Datenobjekts über Stellvertreter-Eigenschaten.

- **Siehe auch:** [Optionen / Daten - data](#data)

### vm.$props

> Neu in 2.2.0+

- **Datentyp:** `Object`

- **Details:**

  Ein Objekt, das die aktuellen Eigenschaften repräsentiert, die die Komponente erhalten hat. Die Vue-Instanz gewährt Zugriff auf die Eigenschaften ihres Datenobjekts über Stellvertreter-Eigenschaten. 

### vm.$el

- **Datentyp:** `HTMLElement`

- **Schreibgeschützt**

- **Details:**

  Das DOM-Wurzelelement, welches von der Vue-Instanz verwaltet wird.

### vm.$options

- **Datentyp:** `Object`

- **Schreibgeschützt**

- **Details:**

  Optionen, die bei der Instanziierung der aktuellen Vue-Instanz verwendet wurden. Dies ist nützlich, wenn Sie benutzerdefinierte Eigenschaften in den Optionen verwenden wollen:

  ``` js
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

### vm.$parent

- **Datentyp:** `Vue instance`

- **Schreibgeschützt**

- **Details:**

  Eltern-Instanz (falls vorhanden).

### vm.$root

- **Datentyp:** `Vue instance`

- **Schreibgeschützt**

- **Details:**

  Die Wurzel-Vue-Instanz des aktuellen Komponentenbaums. Wenn die aktuelle Instanz keine Eltern hat, wird dieser Wert die Instanz selber sein.

### vm.$children

- **Datentyp:** `Array<Vue instance>`

- **Schreibgeschützt**

- **Details:**

  Die direkten Kinder der aktuellen Instanz. Beachten Sie, dass **es keine Garantien bezüglich der Reihenfolge für `$children` gibt und dass die Eigenschaft nicht reaktiv ist.** Wenn Sie versuchen, `$children` für Datenbindung zu verwenden, erwägen Sie lieber den Einsatz eines Arrays und `v-for`, um Kindkomponenten zu generieren. Dann können sie das Array als die Quelle der Wahrheit verwenden.

### vm.$slots

- **Datentyp:** `{ [name: string]: ?Array<VNode> }`

- **Schreibgeschützt**

- **Details:**

  Wird verwendet, um programmatisch auf Inhalte, die über [Slots](../guide/components.html#Content-Distribution-with-Slots) verteilt werden, zuzugreifen. Jedes [benannte Slot](../guide/components.html#Content-Distribution-with-Slots) hat seine eigene zugehörige Eigenschaft (z. B. wird der Inhalt von `slot="foo"` unter `vm.$slots.foo` zu finden sein). Die Eigenschaft `default` enthält all jene Knoten, die in einem der benannten Slots nicht inkludiert sind.

  Am nützlichsten ist der Zugriff über `vm.$slots`, wenn Sie eine Komponente mit einer [Render-Funktion](../guide/render-function.html) schreiben. 

- **Beispiel:**

  ```html
  <blog-post>
    <h1 slot="header">
      About Me
    </h1>

    <p>Hier gibt es einen Inhalt, welcher in vm.$slots.default inkludiert wird, weil er sich nicht innerhalb eines benannten Slots befindet.</p>

    <p slot="footer">
      Copyright 2016 Evan You
    </p>

    <p>Wenn ich hier unten irgendeinen Inhalt habe, wird er ebenfalls in vm.$slots.default inkludiert werden.</p>.
  </blog-post>
  ```

  ```js
  Vue.component('blog-post', {
    render: function (createElement) {
      var header = this.$slots.header
      var body   = this.$slots.default
      var footer = this.$slots.footer
      return createElement('div', [
        createElement('header', header),
        createElement('main', body),
        createElement('footer', footer)
      ])
    }
  })
  ```

- **Siehe auch:**
  - [`<slot>`-Komponente](#slot-1)
  - [Verteilung von Inhalten mit Slots](../guide/components.html#Content-Distribution-with-Slots)
  - [Render-Funktionen - Slots](../guide/render-function.html#Slots)

### vm.$scopedSlots

> Neu in 2.1.0+

- **Datentyp:** `{ [name: string]: props => VNode | Array<VNode> }`

- **Schreibgeschützt**

- **Details:**
  
  Wird verwendet, um programmatisch auf [Scopes mit Gültigkeitsbeschränkung](../guide/components.html#Scoped-Slots) zuzugreifen. Für jedes Slot, inkl. dem `default`-Slot, enthält das Objekt eine entsprechende Funktion, die VNodes zurückgibt.

  Am nützlichsten ist der Zugriff über `vm.$scopedSlots`, wenn Sie eine Komponente mit einer [Render-Funktion](../guide/render-function.html) schreiben. 

- **Siehe auch:**
  - [`<slot>`-Komponente](#slot-1)
  - [Scopes mit Gültigkeitsbeschränkung](../guide/components.html#Scoped-Slots)
  - [Render-Funktionen - Slots](../guide/render-function.html#Slots)

### vm.$refs

- **Datentyp:** `Object`

- **Schreibgeschützt**

- **Details:**

  Ein Objekt, das DOM-Elemente und Instanzen von Komponenten enthält, die mit [`ref`-Attributen](#ref) registriert wurden.

- **Siehe auch:**
  - [Refs der Kindkomponenten](../guide/components.html#Child-Component-Refs)
  - [Besondere Attribute - ref](#ref)

### vm.$isServer

- **Datentyp:** `boolean`

- **Schreibgeschützt**

- **Details:**

  Gibt bekannt, ob die aktuelle Vue-Instanz am Server läuft.

- **Siehe auch:** [Serverseitiges Rendering](../guide/ssr.html)

### vm.$attrs

- **Datentyp:** `{ [key: string]: string }`

- **Schreibgeschützt**

- **Details:**

  Enthält Bindunden aus dem Elternscope (mit Ausnahme von `class` und `style`), die als eigenschaften nicht erkannt (daher auch nicht extrahiert) wurden. Wenn eine Komponente keine deklarierten Eigenschaften hat, enthält `vm.$attrs` alle Bindungen aus dem Eltern-Scope (ausgenommen `class` und `style`) und kann an innere Komponenten via `v-bind="$attrs"` übergeben werden. Dies ist nützlich bei der Entwicklung von Komponenten höherer Ordnung.

### vm.$listeners

- **Datentyp:** `{ [key: string]: Function | Array<Function> }`

- **Schreibgeschützt**

- **Details:**

  Enthält `v-on`-Ereignisbehandlngsroutinen aus dem Eltern-Scope (ohne `.native`-Modifikator). Diese können via `v-on="$listeners"` and eine innere Komponente übergeben werden - nützlich beim Erstellen transparenter Wrapper-Komponenten. 

## Instanzen-Methoden / Daten

### vm.$watch( expOrFn, callback, [options] )

- **Argumente:**
  - `{string | Function} expOrFn`
  - `{Function | Object} callback`
  - `{Object} [options]`
    - `{boolean} deep`
    - `{boolean} immediate`

- **Rückgabewert:** `{Function} unwatch`

- **Verwendung:**

  Beobachtet einen Ausdruck oder eine berechnete Funktion auf der Vue-Instanz, um Veränderungen festzustellen. Das Callback wird mit dem neuen und dem alten Wert aufgerufen. Im Ausdruck sind nur durch einen Punkt getrennte Pfade erlaubt. Für komplexere Ausdrücke können Sie eine Funktion verwenden.

<p class="tip">Anmerkung: Wenn ein Object oder ein Array verändert (nicht ersetzt) wird, ist der alte Wert gleich dem neuen, weil sie auf dasselbe Object/Array verweisen. Vue hat kein Exemplar des Objekts vor der Veränderung.</p>

- **Beispiel:**

  ``` js
  // Schlüsselpfad
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // Hier tun wir etwas 
  })

  // Funktion
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // Hier tun wir etwas 
    }
  )
  ```

  `vm.$watch` gibt eine "unwatch"-Funktion zurück, die das Feuern des Callbacks beendet: 

  ``` js
  var unwatch = vm.$watch('a', cb)
  // Später, beende die Arbeit des Watchers
  unwatch()
  ```

- **Option: deep**

  Um auch Veränderung der verschachtelten Werte innerhalb von Objects feststellen zu können, müssen Sie `deep: true` in das Optionen-Argument übergeben. Beachten Sie, dass dies nicht notwendig ist, wenn Sie über Array-Veränderungen benachrichtigt werden wollen.

  ``` js
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // Callback wird gefeuert
  ```

- **Option: immediate**

  Wenn Sie `immediate: true` an die Option übergeben, wird der Callback unmittelbar danach mit dem aktuellen Wert des Ausdrucks ausgelöst.

  ``` js
  vm.$watch('a', callback, {
    immediate: true
  })
  // `callback` wird sofort mit dem aktuellen Wert von `a` gefeuert
  ```

### vm.$set( target, key, value )

- **Argumente:**
  - `{Object | Array} target`
  - `{string | number} key`
  - `{any} value`

- **Rückgabewert:** Der neue Wert (auf den gesetzt wurde).

- **Verwendung:**

  Dies ist ein **Alias** des globalen `Vue.set`.

- **Siehe auch:** [Vue.set](#Vue-set)

### vm.$delete( target, key )

- **Argumente:**
  - `{Object | Array} target`
  - `{string | number} key`

- **Verwendung:**

  Dies ist ein **Alias** des globalen `Vue.delete`.

- **Siehe auch:** [Vue.delete](#Vue-delete)

## Instanzenmethoden / Ereignisse

### vm.$on( event, callback )

- **Argumente:**
  - `{string | Array<string>} event` (array only supported in 2.2.0+)
  - `{Function} callback`

- **Verwendung:**

  Hört einem benutzerdefinierten Ereignis auf dem aktuellen vm zu. Ereignisse können mit `vm.$emit` ausgelöst werden. Das Callback wird alle zusätzlichen Argumente erhalten, die an diese ereignisauslösende Methoden übergeben wurden.

- **Beispiel:**

  ``` js
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // => "hi"
  ```

### vm.$once( event, callback )

- **Argumente:**
  - `{string} event`
  - `{Function} callback`

- **Verwendung:**

  Höre einem benutzerdefinierten Ereignis zu, aber nur ein Mal. Der Listener wird entfernt, nachdem er zum ersten Mal ausgelöst wurde.

### vm.$off( [event, callback] )

- **Argumente:**
  - `{string | Array<string>} event` (array only supported in 2.2.2+)
  - `{Function} [callback]`

- **Verwendung:**

  Entfernt benutzerdefinierte Event-Listener.

  - Wenn keine Argumente angegeben sind, werden alle Listener entfernt;

  - Wenn nur das Ereignis übergeben wird, werden alle Listener des jeweiligen Ereignisses entfernt;

  - Wenn sowohl das Ereignis, als auch das Callback übergeben werden, wird nur der Listener für das jeweilige Callback entfernt.

### vm.$emit( eventName, [...args] )

- **Argumente:**
  - `{string} eventName`
  - `[...args]`

  Löst ein Ereignis auf der aktuellen Instanz aus. Jegliche zusätzliche Argumente werden an die Callback-Funktion des Listeners übergeben.

- **Beispiele:**

  Verwendung von `$emit` nur mit dem Namen des Ereignisses:

  ```js
  Vue.component('welcome-button', {
    template: `
      <button v-on:click="$emit('welcome')">
        Click me to be welcomed
      </button>
    `
  })
  ```
  ```html
  <div id="emit-example-simple">
    <welcome-button v-on:welcome="sayHi"></welcome-button>
  </div>
  ```
  ```js
  new Vue({
    el: '#emit-example-simple',
    methods: {
      sayHi: function () {
        alert('Hi!')
      }
    }
  })
  ```
  {% raw %}
  <div id="emit-example-simple" class="demo">
    <welcome-button v-on:welcome="sayHi"></welcome-button>
  </div>
  <script>
    Vue.component('welcome-button', {
      template: `
        <button v-on:click="$emit('welcome')">
          Click me to be welcomed
        </button>
      `
    })
    new Vue({
      el: '#emit-example-simple',
      methods: {
        sayHi: function () {
          alert('Hi!')
        }
      }
    })
  </script>
  {% endraw %}

  Verwendung von `$emit` mit zusätzlichen Argumenten:

  ```js
  Vue.component('magic-eight-ball', {
    data: function () {
      return {
        possibleAdvice: ['Yes', 'No', 'Maybe']
      }
    },
    methods: {
      giveAdvice: function () {
        var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
        this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
      }
    },
    template: `
      <button v-on:click="giveAdvice">
        Click me for advice
      </button>
    `
  })
  ```

  ```html
  <div id="emit-example-argument">
    <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
  </div>
  ```

  ```js
  new Vue({
    el: '#emit-example-argument',
    methods: {
      showAdvice: function (advice) {
        alert(advice)
      }
    }
  })
  ```

  {% raw %}
  <div id="emit-example-argument" class="demo">
    <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
  </div>
  <script>
    Vue.component('magic-eight-ball', {
      data: function () {
        return {
          possibleAdvice: ['Yes', 'No', 'Maybe']
        }
      },
      methods: {
        giveAdvice: function () {
          var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
          this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
        }
      },
      template: `
        <button v-on:click="giveAdvice">
          Click me for advice
        </button>
      `
    })
    new Vue({
      el: '#emit-example-argument',
      methods: {
        showAdvice: function (advice) {
          alert(advice)
        }
      }
    })
  </script>
  {% endraw %}

## Instanzenmethoden / Lebenszyklus

### vm.$mount( [elementOrSelector] )

- **Argumente:**
  - `{Element | string} [elementOrSelector]`
  - `{boolean} [hydrating]`

- **Rückgabewert:** `vm` - die Instanz selber

- **Verwendung:**

  Hat die Vue-Instanz bei der Instanziierung keine `el`-Option erhalten, bleibt sie im "unmontierten" Zustand, ohne ein zugehöriges DOM-Element. `vm.$mount()` kann verwendet werden, um manuell das Montieren einer unmontierten Vue-Instanz einzuleiten. 

  Wenn das `elementOrSelector`-Argument nicht übergeben wird, wird die Vorlage als off-document-Element gerendert. Diesfalls werden Sie die native DOM API verwenden müssen, um es selbst in das Dokument einzufügen. 

  Die Methode gibt die Instanz selber zurück, sodass Sie mehrere Methoden verkettet aufrufen können.

- **Beispiel:**

  ``` js
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // Erstelle und montiere auf #app (wird #app ersetzen)
  new MyComponent().$mount('#app')

  // Der Code oben kann auch geschrieben werden als:
  new MyComponent({ el: '#app' })

  // oder, rendere off-document und füge später hinzu
  var component = new MyComponent().$mount()
  document.getElementById('app').appendChild(component.$el)
  ```

- **Siehe auch:**
  - [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)
  - [Serverseitiges Rendering](../guide/ssr.html)

### vm.$forceUpdate()

- **Verwendung:**

  Zwingt die Vue-Instanz dazu, sich neuzurendern. Das betrifft nicht alle Kindkomponenten, nur die Instanz selber und Kindkomponenten mit eingefügtem Slot-Inhalt.

### vm.$nextTick( [callback] )

- **Argumente:**
  - `{Function} [callback]`

- **Verwendung:**
  
  Verschiebe die Ausführung des Callbacks auf die Zeit nach dem nächsten DOM-Update-Zyklus. Verwenden Sie es unmittelbar nachdem Sie Daten verändert haben, um auf den DOM-Update zu warten. Dies hat die gleiche Bedeutung wie die globale `Vue.nextTick`-Methode, ausgenommen, dass der `this`-Kontext des Callbacks automatisch an jene Instanz gebunden wird, die diese Methode aufruft.

  > Neu in 2.1.0+: Gibt ein Promise zurück, wenn kein Callback übergeben wurde und Promise in der Ausführungsumgebung unterstützt wird. Beachten Sie, dass Promise polyfill nicht mit Promise mitgeliefert wird. Wenn Sie Browser ansprechen, die Promises nicht nativ unterstützen (IE), werden sie selber ein polyfill zur Verfügung stellen müssen.

- **Beispiel:**

  ``` js
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // Verändere Daten
        this.message = 'changed'
        // DOM wurde noch nicht aktualisiert
        this.$nextTick(function () {
          // Jetzt ist DOM aktualisiert
          // `this` wurde an die aktuelle Instanz gebunden
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **Siehe auch:**
  - [Vue.nextTick](#Vue-nextTick)
  - [Asychrone Aktualisierungswarteschlange](../guide/reactivity.html#Async-Update-Queue)

### vm.$destroy()

- **Verwendung:**

  Zerstört eine vm komplett. Bereinigt deren Verbindungen zu anderen existierenden vms, löst Bindungen all ihrer Direktiven los, schaltet alle Event-Listener aus.

  Löst `beforeDestroy`- und `destroyed`-Hooks aus.

  <p class="tip">Im Normalfall sollten Sie diese Methode nicht aufrufen. Eine bessere Alternative ist es, den Lebenszyklus der Kindkomponenten auf eine datengetriebene Art und Weise mittels `vif` und `v-for` zu kontrollieren.</p>

- **Siehe auch:** [Lebenszyklusdiagramm](../guide/instance.html#Lifecycle-Diagram)

## Direktiven

### v-text

- **Erwartet:** `string`

- **Details:**

  Aktualisiert den `textContent` eines Elements. Wenn Sie nur einen Teil des `textContent` verändern wollen, sollten Sie Interpolationen `{% raw %}{{ Mustache }}{% endraw %}` verwenden. 

- **Beispiel:**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

- **Siehe auch:** [Syntax für die Datenbindung - Interpolationen](../guide/syntax.html#Text)

### v-html

- **Erwartet:** `string`

- **Details:**

  Verändert `innerHTML` eines Elements. **Beachten Sie, dass die Inhalte als einfaches HTML eingefügt werden -- sie werden nicht als Vue-Vorlagen kompiliert werden**. Wenn Sie versuchen, Vorlagen mittels `v-html` zusammenzusetzen, versuchen Sie, den Ansatz zu überdenken und stattdessen Komponenten zu verwenden.

  <p class="tip">Es ist sehr gefährlich, dynamisch beliebiges HTML-Code auf ihrer Webseite zu rendern, da dies leicht zu [XSS-Attacken](https://de.wikipedia.org/wiki/Cross-Site-Scripting) führen kann. Wenden Sie `v-html` nur auf vertrauenswürdige Inhalt und **niemals** auf benutzergenerierte Inhalte an.</p>

  <p class="tip">In [Ein-Dateien-Komponenten](../guide/single-file-components.html) werden `scoped`-Stile innerhalb von `v-html` nicht angewandt, weil dieses HTML nicht durch den Compiler von Vue verarbeitet wird. Wenn Sie `v-html`-Inhalte mit scoped CSS ansprechen wollen, können Sie stattdessen [CSS-Module](https://vue-loader.vuejs.org/en/features/css-modules.html) oder ein zusätzliches, globales `<style>`-Element mit einer manuellen Strategie der Gültigkeitsbegrenzung wie BEM verwenden. </p>

- **Beispiel:**

  ```html
  <div v-html="html"></div>
  ```

- **Siehe auch:** [Syntax für die Datenbindung - Interpolationen](../guide/syntax.html#Raw-HTML)

### v-show

- **Erwartet:** `any`

- **Verwendung:**

  Schaltet die `display`-CSS-Eigenschaft eines Elements je nach dem, ob der Ausdruck wahr oder falsch ergibt, um.

  Diese Direktive löst Übergänge aus, wenn sich die Bedingung ändert.

- **Siehe auch:** [Bedingtes Rendering - v-show](../guide/conditional.html#v-show)

### v-if

- **Erwartet:** `any`

- **Verwendung:**

  Rendert das Element je nach dem Wahrheitsgehalt des Ausdrucks. Das Element und die darin enthaltenen Direktiven/Komponenten werden beim Umschalten zerstört und neu aufgebaut. Wenn das Element ein `<template>`-Element ist, wird sein Inhalt als bedingter Block extrahiert.

  Diese Direktive löst Übergänge aus, wenn sich die Bedingung ändert.
  
  <p class="tip">Wenn diese Direktive zusammen mit v-for verwendet wird, dann hat v-for eine höhere Priorität als v-if. Für Details siehe <a href="../guide/list.html#v-for-with-v-if">Anleitung zum Rendern von Listen</a>.</p>

- **Siehe auch:** [Bedingtes Rendering - v-if](../guide/conditional.html)

### v-else

- **Erwartet keinen Ausdruck**

- **Einschränkung:** Vorhergehendes "Geschwister"-Element muss `v-if` oder `v-else-if` haben.

- **Verwendung:**

  Bezeichnet den Else-Block für eine `v-if` oder eine `v-if`/`v-else-if`-Kette.

  ```html
  <div v-if="Math.random() > 0.5">
    Jetzt können Sie mich sehen.
  </div>
  <div v-else>
    Jetzt nimmer.
  </div>
  ```

- **Siehe auch:** [Bedingtes Rendering - v-else](../guide/conditional.html#v-else)

### v-else-if

> Neu in 2.1.0+

- **Erwartet:** `any`

- **Einschränkung:** Vorhergehendes "Geschwister"-Element muss `v-if` oder `v-else-if` haben.

- **Verwendung:**

  Bezeichnet den "Else-If"-Block für `v-if`. Kann verkettet werden.

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Weder A, noch B, noch C
  </div>
  ```

- **Siehe auch:** [Bedingtes Rendering - v-else-if](../guide/conditional.html#v-else-if)

### v-for

- **Erwartet:** `Array | Object | number | string`

- **Verwendung:**

  Rendert das Element oder den Vorlagenblock mehrmals, basierend auf den Quelldaten. Der Wert der Direktive muss eine spezielle Syntax `alias in expression` nutzen, um einen Alias für das aktuelle Element in der Schleife anzugeben:

  ``` html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```
  
  Alternativ können Sie auch einen Alias für einen Index (oder ein Schlüssel, wenn es sich um ein Object handelt) angeben:

  ``` html
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, key, index) in object"></div>
  ```

  Das Default-Verhalten von `v-for` besteht darin, die Elemente an ihrem Platz zu patchen, ohne sie zu bewegen. Um eine Sortierung zu erzwingen, müssen Sie einen Ordnungsbegriff mit Hilfe des Sonderattributs `key` angeben:

  ``` html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```
  <p class="tip">Wenn diese Direktive zusammen mit v-if verwendet wird, hat v-for eine höhere Priorität als v-if. Für Details siehe <a href="../guide/list.html#v-for-with-v-if">Anleitung zum Rendern von Listen</a>.</p>

  Die Verwendung von `v-for` ist im Detail in der Anleitung beschrieben, auf die unten verwiesen wird.

- **Siehe auch:**
  - [Rendern von Listen](../guide/list.html)
  - [key](../guide/list.html#key)

### v-on

- **Abkürzung:** `@`

- **Erwartet:** `Function | Inline Statement | Object`

- **Argument:** `event`

- **Modifikatoren:**
  - `.stop` - ruft `event.stopPropagation()` auf.
  - `.prevent` - ruft `event.preventDefault()` auf.
  - `.capture` - fügt einen Event Listener im Abfangmodus hinzu.
  - `.self` - löst einen Handler nur dann aus, wenn das Event von diesem Element emittiert wurde.
  - `.{keyCode | keyAlias}` - löst den Handler nur dann aus, wen bestimmte Tasten gedrückt wurden.
  - `.native` - "Lausche" einem nativen Ereignis auf dem Wurzelelement der Komponente.
  - `.once` - löst den Handler höchstens ein Mal aus.
  - `.left` - (2.2.0+) löst den Handler nur bei Events in Verbindung mit der linken Maustaste.
  - `.right` - (2.2.0+) löst den Handler nur bei Events in Verbindung mit der rechten Maustaste.
  - `.middle` - (2.2.0+) löst den Handler nur bei Events in Verbindung mit der mittleren Maustaste.
  - `.passive` - (2.3.0+) bringt einen DOM-Event mit `{ passive: true }` an.

- **Verwendung:**

  Bringt einen Event-Listener an ein Element an. Der Typ des Events wird durch das Argument bestimmt. Der Ausdruck kann ein Methodenname, ein Inline-Statement sein, oder ausgelassen werden, wenn Modifikatoren vorhanden sind.

  Wenn die Direktive auf ein normales Element angewandt wird, hört der Event-Listener nur [**nativen DOM-Ereignissen**](https://developer.mozilla.org/en-US/docs/Web/Events) zu. Wenn sie auf eine benutzerdefinierte Elementkomponente angewandt wird, hört der Listener **benutzerdefinierten ereignissen** zu, die auf dieser Kindkomponente emittiert wurden.

  Wenn sie nativen DOM-Events zuhört, erhält die Methode den nativen Event als das einzige Argument. Wenn ein Inline-Statement verwendet wird, hat es Zugriff zur besonderen `$event`-Eigenschaft: `v-on:click="handle('ok', $event)"`.

  Ab Version 2.4.0+ unterstützt `v-on` auch Bindung an ein Objekt von Ereignis-Listener-Paaren ohne ein Argument. Beachten Sie, dass die Objektsyntax keinerlei Modifikatoren unterstützt.

- **Beispiel:**

  ```html
  <!-- Methoden-Handler -->
  <button v-on:click="doThis"></button>

  <!-- Inline-Statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- Abkürzung -->
  <button @click="doThis"></button>

  <!-- Weiterleitung beenden -->
  <button @click.stop="doThis"></button>

  <!-- Default-Verhalten verhindern -->
  <button @click.prevent="doThis"></button>

  <!-- Default-Verhalten ohne Ausdruck verhindern -->
  <form @submit.prevent></form>

  <!-- Modifikatoren verketten -->
  <button @click.stop.prevent="doThis"></button>

  <!-- Tastenmodifikatoren mittels keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- Tastenmodifikatoren mittels keyCode -->
  <input @keyup.13="onEnter">

  <!-- Das Klick-Ereignis wird höchstens ein Mal ausgelöst -->
  <button v-on:click.once="doThis"></button>

  <!-- Die Objektsyntax (2.4.0+) -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  So hört man benutzerdefinierten Ereignissen auf einer Kindkomponente zu (der Handler wird aufgerufen, wenn "my-event" auf dem Kind emittiert wird): 

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- Inline-Statement -->
  <my-component @my-event="handleThis(123, $event)"></my-component>

  <!-- Natives Ereignis auf der Komponente -->
  <my-component @click.native="onClick"></my-component>
  ```

- **Siehe auch:**
  - [Ereignisbehandlung](../guide/events.html)
  - [Komponenten - Benutzerdefinierte Ereignisse](../guide/components.html#Custom-Events)

### v-bind

- **Abkürzung:** `:`

- **Erwartet:** `any (mit Argument) | Object (ohne Argument)`

- **Argument:** `attrOrProp (optional)`

- **Modifikatoren:**
  - `.prop` - Binde als DOM-Eigenschaft statt eines Attributs ([what's the difference?](https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028)). Wenn der Tag eine Komponente ist, dann wird `.prop` die Eigenschaft auf das `$el` der Komponente setzen.
  - `.camel` - (2.1.0+) Wandle den Namen des Attributs von kebab-case in camelCcase um. 
  - `.sync` - (2.3.0+) Syntaktischer Zucker, welcher in ein `v-on`-Handler zum Aktualisieren des gebundenen Wertes expandiert wird.

- **Verwendung:**

  Bindet dynamisch eine oder mehrere Attribute, oder eine Eigenschaft der Komponente an einen Ausdruck an.

  Wenn die Direktive verwendet wird, um die `class`- oder `style`-Attribute zu binden, unterstützt sie zusätzliche Wertetypen wie Arrays oder Objects. Siehe den Verweis auf einen Abschnitt der Anleitung unten für mehr Details.

  Wenn sie zum Binden von Eigenschaften verwendet wird, muss die Eigenschaft in der Kindkomponente korrekt deklariert sein.

  Bei Verwendung ohne Argument, kann die Direktive verwendet werden, um ein Objekt mit Paaren (Attributname, Wert) zu binden. Beachten Sie, dass in diesem Modus `class` und `style` keine Arrays oder Objects unterstützen.

- **Beispiel:**

  ```html
  <!-- Ein Attribut binden -->
  <img v-bind:src="imageSrc">

  <!-- Abkürzung -->
  <img :src="imageSrc">

  <!-- Inline-Stringverkettung -->
  <img :src="'/path/to/images/' + fileName">

  <!-- Klassenbindung -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- Stile binden -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- Objekt mit Attributen binden -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- DOM-Attribute mit einem Eigenschaften-Modifikator binden -->
  <div v-bind:text-content.prop="text"></div>

  <!-- Eigenschaften binden. "prop" muss in my-component deklariert werden. -->
  <my-component :prop="someThing"></my-component>

  <!-- Eltern-Eigenschaften an Kindkomponenten übergeben. -->
  <child-component v-bind="$props"></child-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```
  
  Der `.camel`-Modifikator erlaubt es, einen `v-bind`-Attributnamen in nicht-DOM-Vorlagen zu "kamelisieren", z. B. SVG `viewBox`-Attribut:

  ``` html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` wird nicht benötigt, wenn Sie Stringvorlagen verwenden, oder mit `vue-loader`/`vueify` kompilieren.

- **Siehe auch:**
  - [Klassen- und Stilbindungen](../guide/class-and-style.html)
  - [Komponenten - Eigenschaften](../guide/components.html#Props)
  - [Komponenten - `.sync`-Modifikator](../guide/components.html#sync-Modifier)

### v-model

- **Erwartet:** Unterschiedlich je nach Wert des Formularelements oder Ausgabe der Komponenten

- **Begrenzt auf:**
  - `<input>`
  - `<select>`
  - `<textarea>`
  - Komponenten

- **Modifikatoren:**
  - [`.lazy`](../guide/forms.html#lazy) - höre statt `input`-, `change`-Ereignissen zu
  - [`.number`](../guide/forms.html#number) - wandle den Eingabestring in Zahlen um
  - [`.trim`](../guide/forms.html#trim) - trimme den Eingabestring 

- **Verwendung:**

  Erstellt eine Bindung in zwei Richtungen auf einem Formularelement oder einer Komponente. Für Details bezüglich Verwendung und andere Anmerkungen siehe den Abschnitt der Anleitung, auf den unten verwiesen wird. 

- **Siehe auch:**
  - [Bindungen für Formularfelder](../guide/forms.html)
  - [Komponenten - Eingabekomponenten für Formulare mit benutzerdefinierten Ereignissen](../guide/components.html#Form-Input-Components-using-Custom-Events)

### v-pre

- **Erwartet keinen Ausdruck**

- **Verwendung:**

  Überspring die Kompilierung für dieses Element und alle seine Kinder. Sie können dies verwenden, um rohe "mustache tags" auszugeben. Das Überspringen einer hohen Anzahl von Knoten, die keine Direktiven haben, kann auch die Kompilierung beschleunigen.

- **Beispiel:**

  ```html
  <span v-pre>{{ Das wird nicht kompiliert }}</span>
   ```

### v-cloak

- **Erwartet keinen Ausdruck**

- **Verwendung:**

  Diese Direktive wird auf dem Element bleiben, bis die damit verbundene Vue-Instanz fertigkompiliert wurde. In Verbindung mit solchen CSS-Regeln wie `[v-cloak] {display: none}`, kann diese Direktive verwendet werden, um un-kompilierte Mustache-Bindungen zu verstecken, bis die Vue-Instanz bereit ist.

- **Beispiel:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  Das `<div>`-Element wird nicht sichtbar sein, bis die Kompilierung abgeschlossen ist.

### v-once

- **Erwartet keine Ausdrücke**

- **Details:**

  Rendert das Element und die Komponente nur **ein Mal**. Bei darauf folgenden neu-Renders wird das Element (die Komponente) und alle ihre Kinder als statische Inhalt behandelt und daher übersprungen. Dies kann verwendet werden, um die Performance der Updates zu verbessern.  

  ```html
  <!-- Einzelnes Element -->
  <span v-once>Dies wird sich niemals ändern: {{msg}}</span>
  <!-- Das Element hat Kinder -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for`-Direktive-->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **Siehe auch:**
  - [Syntax für die Datenbindung - Interpolationen](../guide/syntax.html#Text)
  - [Komponenten - Billige statische Komponenten mit `v-once`](../guide/components.html#Cheap-Static-Components-with-v-once)

## Besondere Attribute

### key

- **Erwartet:** `number | string`

  Das `key`-Sonderattribut wird primär als Hinweis für den virtuellen DOM-Algorithmus von Vue... genutzt, um VNodes beim Synchronisieren von Knotenlisten zu identifizieren. Ohne Schlüssel verwendet Vue einen Algorithmus, welches die Anzahl der Elementenbewegungen minimiert und versucht, wo es möglich ist, Elemente des gleichen Typs an ihrer Stelle zu patchen (wiederzuverwenden). Wenn der/die Schlüssel angegeben wird/werden, wird der Algorithmus die Elemente anhand der Schlüssel anordnen. Elemente mit Schlüsseln, die nicht mehr vorhanden sind, werden entfernt (zerstört).

  Kinder des gleichen gemeinsamen Eltern-Elements müssen **einzigartige Schlüssel** haben. Doppelte Schlüssel werden zu Renderfehlern führen.

  Der häufigste Anwendungsfall ist mit `v-for` verbunden:

  ``` html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  Es kann auch verwendet werden, um die Ersetzung von Elementen/Komponenten zu erzwingen (statt sie wiederzuverwenden). Dies kann sinnvoll sein, wenn Sie

  - Lebenszyklus-Hooks einer Komponente korrekt auslösen oder 
  - Übergänge auslösen 

  wollen. 

  Zum Beispiel:

  ``` html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  Wenn sich `text` verändert, wird `<span>` immer ersetzt (nicht gepatched) werden, daher wird ein Übergang ausgelöst. 

### ref

- **Erwartet:** `string`

  `ref` wird verwendet, um einen Verweis auf ein Element oder eine Kindkomponente zu registrieren. Der Verweis wird unter dem Objekt `$refs` der Elternkomponente registriert. Wenn dies auf ein einfaches DOM-Element angewandt wird, wird der Verweis zu diesem Element. Wenn dies auf eine Kindkomponente angewandt wird, wird der Verweis eine Komponenteninstanz sein:

  ``` html
  <!-- vm.$refs.p wird der DOM-Knoten sein -->
  <p ref="p">hello</p>

  <!-- vm.$refs.child wird die Kindkomponente sein -->
  <child-component ref="child"></child-component>
  ```

  Wenn es auf Elemente/Komponenten mit `v-for` angewandt wird, wird der registrierte Verweis ein Array sein, welches DOM-Knoten oder Komponenteninstanzen enthält. 

  Eine wichtige Anmerkung über den zeitlichen Ablauf der ref-Registrierung: Weil die refs selber als Ergebnis einer Render-Funktion erstellt werden, können Sie auf sie nicht beim ersten Render-Durchlauf zugreifen - sie existieren noch nicht! Außerdem ist `$refs` nicht-reaktiv, daher sollten Sie nicht versuchen, sie in Vorlagen für die Datenbindung zu nutzen.

- **Siehe auch:** [Verweise auf Kindkomponenten](../guide/components.html#Child-Component-Refs)

### slot

- **Erwartet:** `string`

  Wird auf Inhalte angewandt, die in Kindkomponenten eingefügt werden, um zu zeigen, zu welchem benannten Slot der Inhalt gehört.

  Für detaillierte Beschreibung, wie man das verwendet, siehe untenstehenden Verweis auf einen Abschnitt in der Anleitung.

- **Siehe auch:** [Benannte Slots](../guide/components.html#Named-Slots)

### slot-scope

> Neu in 2.5.0+

- **Expects:** `Funktionsargument-Ausdruck`

- **Verwendung:**

  Wird verwendet, um ein Element oder eine Komponente als einen Slot mit Gültigkeitsbereichbeschränkung zu markieren. Der Wert des Attributs sollte ein gültiger JavaScript-Ausdruck sein, welcher als Argument in einer Funktionssignatur erscheinen darf. Das bedeutet, dass sie auch ES2015 dstructuring verwenden können, sofern dies von der Ausführungsumgebung unterstützt wird. Dient als Ersatz für [`scope`](#scope-replaced) in 2.5.0+. 

  Dieses Attribut unterstützt keine dynamische Bindung.

- **Siehe auch:** [Slots mit Gültigkeitsbereichsbeschränkung](../guide/components.html#Scoped-Slots)

### scope <sup>replaced</sup>

Wird verwendet, um ein Element einer `<template>` als einen Slot mit Begrenzung des Gültigkeitsbereichs zu markieren. Dies wurde in 2.5.0+ durch [`slot-scope`](#slot-scope) ersetzt. 

- **Verwendung:**

  Gleichbedeutend mit [`slot-scope`](#slot-scope), außer dass `scope` nur auf `<template>`-Elemente angewandt werden kann. 

### is

- **Erwartet:** `string | Object (Optionenobjekt der Komponente)`

  Wird für [dynamische Komponenten](../guide/components.html#Dynamic-Components) verwendet, sowie als Workaround für [Begrenzungen der in-DOM-Vorlagen](../guide/components.html#DOM-Template-Parsing-Caveats). 

  Zum Beispiel:

  ``` html
  <!-- component verändert sich, wenn sich currentView verändert -->
  <component v-bind:is="currentView"></component>

  <!-- Dies ist notwendig, weil `<my-row>` innerhalb eines  --> 
  <!-- `<table>`-Elements ungültig und deswegen herausgezogen -->
  <!-- werden würde. -->
  <table>
    <tr is="my-row"></tr>
  </table>
  ```

  Folgen Sie den Links unten, um mehr über die Verwendung dieses Attributs zu erfahren. 

- **Siehe auch:**
  - [Dynamische Komponenten](../guide/components.html#Dynamic-Components)
  - [Vorbehalte beim Parsing von DOM-Vorlagen](../guide/components.html#DOM-Template-Parsing-Caveats)

## Built-In Components

### component

- **Props:**
  - `is` - string | ComponentDefinition | ComponentConstructor
  - `inline-template` - boolean

- **Verwendung:**

  A "meta component" for rendering dynamic components. The actual component to render is determined by the `is` prop:

  ```html
  <!-- a dynamic component controlled by -->
  <!-- the `componentId` property on the vm -->
  <component :is="componentId"></component>

  <!-- can also render registered component or component passed as prop -->
  <component :is="$options.components.child"></component>
  ```

- **Siehe auch:** [Dynamic Components](../guide/components.html#Dynamic-Components)

### transition

- **Props:**
  - `name` - string, Used to automatically generate transition CSS class names. e.g. `name: 'fade'` will auto expand to `.fade-enter`, `.fade-enter-active`, etc. Defaults to `"v"`.
  - `appear` - boolean, Whether to apply transition on initial render. Defaults to `false`.
  - `css` - boolean, Whether to apply CSS transition classes. Defaults to `true`. If set to `false`, will only trigger JavaScript hooks registered via component events.
  - `type` - string, Specify the type of transition events to wait for to determine transition end timing. Available values are `"transition"` and `"animation"`. By default, it will automatically detect the type that has a longer duration.
  - `mode` - string, Controls the timing sequence of leaving/entering transitions. Available modes are `"out-in"` and `"in-out"`; defaults to simultaneous.
  - `enter-class` - string
  - `leave-class` - string
  - `appear-class` - string
  - `enter-to-class` - string
  - `leave-to-class` - string
  - `appear-to-class` - string
  - `enter-active-class` - string
  - `leave-active-class` - string
  - `appear-active-class` - string

- **Events:**
  - `before-enter`
  - `before-leave`
  - `before-appear`
  - `enter`
  - `leave`
  - `appear`
  - `after-enter`
  - `after-leave`
  - `after-appear`
  - `enter-cancelled`
  - `leave-cancelled` (`v-show` only)
  - `appear-cancelled`

- **Verwendung:**

  `<transition>` serve as transition effects for **single** element/component. The `<transition>` only applies the transition behavior to the wrapped content inside; it doesn't render an extra DOM element, or show up in the inspected component hierarchy.

  ```html
  <!-- simple element -->
  <transition>
    <div v-if="ok">toggled content</div>
  </transition>

  <!-- dynamic component -->
  <transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </transition>

  <!-- event hooking -->
  <div id="transition-demo">
    <transition @after-enter="transitionComplete">
      <div v-show="ok">toggled content</div>
    </transition>
  </div>
  ```

  ``` js
  new Vue({
    ...
    methods: {
      transitionComplete: function (el) {
        // for passed 'el' that DOM element as the argument, something ...
      }
    }
    ...
  }).$mount('#transition-demo')
  ```

- **Siehe auch:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

### transition-group

- **Props:**
  - `tag` - string, defaults to `span`.
  - `move-class` - overwrite CSS class applied during moving transition.
  - exposes the same props as `<transition>` except `mode`.

- **Events:**
  - exposes the same events as `<transition>`.

- **Verwendung:**

  `<transition-group>` serve as transition effects for **multiple** elements/components. The `<transition-group>` renders a real DOM element. By default it renders a `<span>`, and you can configure what element is should render via the `tag` attribute.

  Note every child in a `<transition-group>` must be **uniquely keyed** for the animations to work properly.

  `<transition-group>` supports moving transitions via CSS transform. When a child's position on screen has changed after an updated, it will get applied a moving CSS class (auto generated from the `name` attribute or configured with the `move-class` attribute). If the CSS `transform` property is "transition-able" when the moving class is applied, the element will be smoothly animated to its destination using the [FLIP technique](https://aerotwist.com/blog/flip-your-animations/).

  ```html
  <transition-group tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
  ```

- **Siehe auch:** [Transitions: Entering, Leaving, and Lists](../guide/transitions.html)

### keep-alive

- **Props:**
  - `include` - string or RegExp or Array. Only components matched by this will be cached.
  - `exclude` - string or RegExp or Array. Any component matched by this will not be cached.

- **Verwendung:**

  When wrapped around a dynamic component, `<keep-alive>` caches the inactive component instances without destroying them. Similar to `<transition>`, `<keep-alive>` is an abstract component: it doesn't render a DOM element itself, and doesn't show up in the component parent chain.

  When a component is toggled inside `<keep-alive>`, its `activated` and `deactivated` lifecycle hooks will be invoked accordingly.

  > In 2.2.0+ and above, `activated` and `deactivated` will fire for all nested components inside a `<keep-alive>` tree.

  Primarily used with preserve component state or avoid re-rendering.

  ```html
  <!-- basic -->
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>

  <!-- multiple conditional children -->
  <keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </keep-alive>

  <!-- used together with `<transition>` -->
  <transition>
    <keep-alive>
      <component :is="view"></component>
    </keep-alive>
  </transition>
  ```

  Note, `<keep-alive>` is designed for the case where it has one direct child component that is being toggled. It does not work if you have `v-for` inside it. When there are multiple conditional children, as above, `<keep-alive>` requires that only one child is rendered at a time.

- **`include` and `exclude`**

  > New in 2.1.0+

  The `include` and `exclude` props allow components to be conditionally cached. Both props can be a comma-delimited string, a RegExp or an Array:

  ``` html
  <!-- comma-delimited string -->
  <keep-alive include="a,b">
    <component :is="view"></component>
  </keep-alive>

  <!-- regex (use `v-bind`) -->
  <keep-alive :include="/a|b/">
    <component :is="view"></component>
  </keep-alive>

  <!-- Array (use `v-bind`) -->
  <keep-alive :include="['a', 'b']">
    <component :is="view"></component>
  </keep-alive>
  ```

  The match is first checked on the component's own `name` option, then its local registration name (the key in the parent's `components` option) if the `name` option is not available. Anonymous components cannot be matched against.

  <p class="tip">`<keep-alive>` does not work with functional components because they do not have instances to be cached.</p>

- **Siehe auch:** [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### slot

- **Props:**
  - `name` - string, Used for named slot.

- **Verwendung:**

  `<slot>` serve as content distribution outlets in component templates. `<slot>` itself will be replaced.

  For detailed usage, see the guide section linked below.

- **Siehe auch:** [Content Distribution with Slots](../guide/components.html#Content-Distribution-with-Slots)

## VNode Interface

- Please refer to the [VNode class declaration](https://github.com/vuejs/vue/blob/dev/src/core/vdom/vnode.js).

## Server-Side Rendering

- Please refer to the [vue-server-renderer package documentation](https://github.com/vuejs/vue/tree/dev/packages/vue-server-renderer).
