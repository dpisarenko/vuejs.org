---
title: Styleguide
type: style-guide
---

Dies ist das offizielle Styleguide für Vue-spezifischen Code. Es ist eine gute Referenz um Fehler, Bikeshedding und Antimuster zu vermeiden. Allerdings ist kein Styleguide ideal für jedes Team und jedes Projekt. Aus diesem Grund sind Abweichungen, basierend auf Erfahrung, Umgebung und persönlichen Werten, willkommen.

Wir versuchen größtenteils auf allgemeine Hinweise bezüglich JavaScript und HTML zu verzichten. Wir haben nichts dagegen wenn du Semikolons oder nachgestellte Kommas benutzten willst. Es ist uns egal, ob du einfache Anführungszeichen oder doppelte für absolute Werte in HTML verwenden willst. Es gibt allerdings einige Ausnahmen, in denen wir festgestellt haben, dass bestimmte Muster im Kontext von Vue hilfreich sind.

> **Wir werden bald auch Tips für Durchsetzung bereitstellen.** Manchmal wirst du einfach diszipliniert sein müssen. Wenn möglich, werden wir aber zeigen wie du mit ESLint und anderen automatischen Prozessen die Durchsetzung vereinfachen kannst.

Zuletzt haben wir unsere Regeln in vier Kategorien eingeteilt:

## Regelkategorien

### Priorität A: Notwendig

Diese Regeln verhindern Fehler. Du solltest sie lernen und befolgen. Ausnahmen können existieren, sind aber selten und sollten nur von Experten in JavaScript und Vue gemacht werden.

### Priorität B: Stark empfohlen

Diese Regeln verbessern in den meisten Projekten die Lesbarkeit und/oder das Entwicklererlebnis. Dein Quelltext wird laufen auch wenn di sie nicht befolgst. Abweichungen sollten aber selten und gerechtfertigt sein.

### Priorität C: Empfohlen

Hier gibt es mehrere gleichwertige Möglichkeiten. Deine Wahl sollte konstant bleiben. In diesen Regeln werden wir alle akzeptierten Optionen beschreiben und einen Standard empfehlen. Du kannst also innerhalb deiner Codebase frei entscheiden, solange du konstant bleibst und einen guten Grund hast. Du solltest wirklich einen guten Grund haben! Wenn du den Standard der Community übernimmst, wirst du:

1. Den Code der Community schneller nachvollziehen können
2. Den Code der Community ohne Änderungen übernehmen können
3. Neue Angestelle werden häufiger deinen Standard bereits kennen, zumindest im Bezug auf Vue 

### Priorität D: Auf eigene Gefahr

Einige Features in Vue existieren für seltene Grenzfälle oder für Migrationen von älteren Versionen. Wenn du sie zu häufig benutzt, kann es zu mehr Wartungsaufwand und sogar Bugs führen. Diese Regeln beleuchten potentiell riskante Features und beschreiben wann und warum man sie vermeiden sollte.

## Priorität A Regeln: Notwendig (Fehlervermeidung)

### Komponentennamen aus mehreren Wörtern <sup data-p="a">Notwendig</sup>

**Komponentennamen sollten immer aus mehreren Wörtern bestehen, mit Ausnahme der `App` Wurzelkomponenten.**

Das [vermeidet Konflikte](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) mit vorhandenden und zukünftigen HTML-Elementen, weil alle HTML-Elemente aus einzelnen Wörtern bestehen.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
Vue.component('todo', {
  // ...
})
```

``` js
export default {
  name: 'Todo',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
Vue.component('todo-item', {
  // ...
})
```

``` js
export default {
  name: 'TodoItem',
  // ...
}
```
{% raw %}</div>{% endraw %}

### Komponentendaten <sup data-p="a">Notwendig</sup>

**Das Attribut `data` in einer Komponente muss eine Funktion sein.**

Wenn das Attribut `data` in einer Komponente benutzt wird (d.h. überall außer `new Vue`) muss der Wert eine Funktion sein, welche ein Objekt zurück gibt.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Wenn der Wert von `data` ein Objekt ist, dann wird es über alle Instanzen der Komponente geteilt. Stell dir eine Komponente `TodoList` mit diesen Daten vor:

``` js
data: {
  listTitle: '',
  todos: []
}
```

Möglicherweise wollen wir diese Komponente mehrmals verwenden um dem Anwender zu erlauben mehrere Listen (bspw. für Einkäufe, Wishlists, Tägliche Aufgaben, ...) zu pflegen. Hier ergibt sich ein Problem. Jede Instanz referenziert das selbe Objekt und wenn der Titel einer Liste geändert wird, ändert es den Titel jeder anderen Liste. Gleiches gilt für hinzufügen/editieren/löschen von einem Element in `todos`.

Stattdessen wollen wir, dass jede Komponente ihre eigenen Daten verwaltet. Um das zu erreichen muss jede Instanz ein eigenes Datenobjekt erzeugen. In JavaScript kann das umgesetzt werden indem man das Objekt in einer Funktion zurückgibt:

``` js
data: function () {
  return {
    listTitle: '',
    todos: []
  }
}
```
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
Vue.component('some-comp', {
  data: {
    foo: 'bar'
  }
})
```

``` js
export default {
  data: {
    foo: 'bar'
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut
``` js
Vue.component('some-comp', {
  data: function () {
    return {
      foo: 'bar'
    }
  }
})
```

``` js
// In a .vue file
export default {
  data () {
    return {
      foo: 'bar'
    }
  }
}
```

``` js
// Es ist okay ein Objekt auf der Wurzelinstanz 
// von Vue zu benutzen weil es nur diese eine 
// Instanz geben kann.
new Vue({
  data: {
    foo: 'bar'
  }
})
```
{% raw %}</div>{% endraw %}



### Props definieren <sup data-p="a">Notwendig</sup>

**Props sollten so detailiert wie möglich definiert werden.**

In einer Produktivumgebung sollten Props immer so detailiert wie möglich, zumindest mit Datentyp, beschrieben werden.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Detailierte [Prop-Definitionen](https://vuejs.org/v2/guide/components.html#Prop-Validation) haben zwei Vorteile:

- Sie dokumentieren die API der Komponente, sodass es einfacher ist nachzuvollziehen wie diese benutzt werden soll.
- Während der Entwicklung wird Vue dich warnen wenn an die Komponenten Daten im falschen Format übergeben werden und sommit Fehlerquellen reduzieren.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
// Das ist nur beim Prototyping okay
props: ['status']
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
props: {
  status: String
}
```

``` js
// Noch besser!
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```
{% raw %}</div>{% endraw %}



### Keyed `v-for` <sup data-p="a">Notwendig</sup>

**Es sollte immer `key` mit `v-for` benutzt werden.**

`key` mit `v-for` ist _immer_ notwendig in Komponenten um den internen Status einer Komponente bis zum Subtree beibehalten zu können. Selbst für Elemente ist es vorteilhaft ein vorhersehbares Verhalten zu pflegen wie bspw. [Objektkonstanz](https://bost.ocks.org/mike/constancy/) in Animationen.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Sagen wir du hast eine Todo-Liste:

``` js
data: function () {
  return {
    todos: [
      {
        id: 1,
        text: 'Learn to use v-for'
      },
      {
        id: 2,
        text: 'Learn to use key'
      }
    ]
  }
}
```

Danach sortierst du sie alphabetisch. Vue sucht nach der günstigten Form das DOM zu aktualisieren, d.h. möglicherweise das Elemente gelöscht und wieder hinzugefügt werden.

Das Problem ist, dass es Fälle gibt in denen es wichtig ist keine Elemente zu löschen, die im DOM bleiben. Möglicherweise willst du `<transition-group>` nutzen um die Sortierung zu animieren oder den Fokus für `<input>` beibehalten. In diesen Fällen kann ein eindeutiger Schlüssel (z.B. `:key="todo.id"`) Vue dabei helfen sich mehr vorhersehbarer zu verhalten.

Wir emfehlen _immer_ ein eindeutigen Schlüssel hinzuzufügen, sodass du und dein Team diese Randfälle nicht beachten müssen. In seltenen, performanzkritischen Fällen, wenn Objektkonsistenz nicht wichtig ist, kannst du eine bewusste Ausnahme machen.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}



### Vermeide `v-if` mit `v-for` <sup data-p="a">Notwendig</sup>

**Benutze nie `v-if` auf dem selben Element mit `v-for`.**

Es gibt zwei Fälle in denen das verlockend sein mag:

- Um eine Liste zu filtern (z.B. `v-for="user in users" v-if="user.isActive"`). In diesen Fällen, ersetze `users` mit einer neuen berechneten Eigenschaft, die eine gefilterte Liste zurück gibt (z.B `activeUsers`).

- Um eine Liste nicht zu rendern, wenn diese versteckt werden soll (z.B. `v-for="user in users" v-if="shouldShowUsers"`). In diesen Fällen solltest du das `v-if` auf einem Container-ELement anwenden (z.B `ul`, `ol`).

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Wenn Vue Direktive verarbeitet  hat `v-for` eine höhre Priorität als `v-if`, sodass dieses Template:

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

evaluiert wird zu:

``` js
this.users.map(function (user) {
  if (user.isActive) {
    return user.name
  }
})
```

Somit wird jedes Mal die gesamte Liste iteriert auch wenn wir nur einen Teil rendern wollen.

Wenn wir stattdessen über eine berechneten Eigenschaft iterieren:

``` js
computed: {
  activeUsers: function () {
    return this.users.filter(function (user) {
      return user.isActive
    })
  }
}
```

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

Kriegen wir folgende Vorteile:

- Die gefilterte Liste wird nur dann neu ausgewertet wenn es relevante Änderungen in `users` gibt. Damit ist das Filtern wesentlich effizienter.
- Wenn wir `v-for="user in activeUsers"` benutzen, dann iterieren wir nur aktive User beim Rendern. Damit ist das Rendering wesentlich effizienter.
- Logik ist entkoppelt von der Präsentation wodurch Wartung wesentlich einfacher wird.

Ähnliche Vorteile krigen wir durch das Ändern von:

``` html
<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

zu:

``` html
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

Wenn das `v-if` auf  ein Containerelement verschoben wird, wird `shouldShowUsers` nicht für _jeden_ User geprüft. Stattdessen prüfen wir es einmal und `v-for` wird überhaupt nicht evaluiert, wenn `shouldShowUsers` false ist.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

``` html
<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

``` html
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```
{% raw %}</div>{% endraw %}

### Style-Scoping in Komponenten <sup data-p="a">Notwendig</sup>

**Styles in der Top-Level-Komponente und in Layout-Komponenten können globale Gültigkeit haben. Alle anderen sollten auf die jeweilige Komponente beschränkt sein.**

Das ist nur für [1-Datei-Komponenten](../guide/single-file-components.html) relevant. Es muss _nicht_ zwingend das [Attribut `scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html) benutzt werden. Es können auch [CSS-Module](https://vue-loader.vuejs.org/en/features/css-modules.html), eine klassenbasierte Lösung wie [BEM](http://getbem.com/) oder andere Libraries benutzt werden.

**Komponentenbibliotheken sollten eine klassenbasierte Lösung bevorzugen statt das Attribut `scoped` zu benutzen.**

Das macht das Überschreiben interner Styles einfacher mit gut lesbaren Klassennamen, die nicht zu spezifisch sind und gleichzeitig wenig Potential für Konflikte bieten.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Wenn du ein größeres Projekt hast, mit andren zusammenarbeitest oder HTML/CSS von Drittanbietern (z.B. Auth0) einfügst wird konsistentes Scoping sicherstellen, dass deine Styles nur auf Komponenten angewandt werden für die sie bestimmt sind.

Außer dem Attribut `scoped` kann man mit eindeutigen Klassennamen sicherstellen, dass CSS von Drittanbietern nicht auf dein eigenes HTML angewandt wird. Viele Projekte nutzen bspw. die Klassen `button`, `btn` und `icon` sodass, auch wenn du nicht gleich eine Strategie wie BEM einsetzt, applikations- und/oder komponentenspezifische Vorsilben (z.B `ButtonClose-icon`) einen gewissen Schutz bieten können.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<template>
  <button class="button button-close">X</button>
</template>

<!-- Using the `scoped` attribute -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

``` html
<template>
  <button :class="[$style.button, $style.buttonClose]">X</button>
</template>

<!-- Using CSS modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

``` html
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

<!-- Using the BEM convention -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Namen privater Eigenschaften <sup data-p="a">Notwendig</sup>

**Nutze immer das Präfix `$_` für benutzerdefinierte private Eigenschaften in einem Plugin, Mixin, etc. Um zusätzlich Konflikte mit anderen Autoren zu vermeiden, sollte ein Scope hinzugefügt werden (z.B. `$_yourPluginName_`).**

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Vue nutzt das Präfix `_` um eigene private Eigenschaften zu definieren. Du würdest also riskieren eine Instanzeigenschaft zu überschreiben, solltest du das gleiche Präfix (z.B. `_update`) nutzen. Selbst wenn du sicherstellst, dass ein bestimmter Name nicht bereitst benutzt wird, kann es trotzdem zu Konflikten mit zukünftigen Versionen führen.

Das Präfix `$` wird von Vue für besondere Eigenschaften benutzt, die für den User zugänglich sind. Es würde sich also nicht für benutzerdefinierte private EIgenschaften eignen.

Stattdessen empfehlen wir die zwei zu kombinieren `$_` für benutzerdefinierte private Eigenschaften um Konflikte mit Vue zu vermeiden.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
var myGreatMixin = {
  // ...
  methods: {
    update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    _update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_update: function () {
      // ...
    }
  }
}
```

{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update: function () {
      // ...
    }
  }
}
```
{% raw %}</div>{% endraw %}



## Priorität B Regeln: Stark empfohlen (Lesbarkeit verbessern)



### Komponentendateien <sup data-p="b">stark empfohlen</sup>

**Wenn ein Build-System vorhanden ist um die Dateien zu verknüpfen, sollte jede Komponente in einer eigenen Datei sein.**

Dies hilft dir eine Komponente schneller zu finden wenn du sie editieren oder nachschauen willst wie sie funktioniert.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
Vue.component('TodoList', {
  // ...
})

Vue.component('TodoItem', {
  // ...
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```
{% raw %}</div>{% endraw %}



### Namenskonventionen für 1-Datei-Komponenten <sup data-p="b">stark empfohlen</sup>

**Dateinamen von [1-Datei-Komponenten](../guide/single-file-components.html) sollten entweder immer PascalCase oder immer kebab-case folgen.**

PascalCase funktioniert am Besten für Autovervollständigung in Editoren, weil es konsistent ist mit der Referenzierung von Komponenten in JS(X) und Templates. Gemischte Groß/Keinschreibung können zu Konflikten auf Systemen führen, die zwischen Groß- und Kleinschreibung nicht unterscheiden, wesshalb kebab-case eine legitime Wahl ist.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
{% raw %}</div>{% endraw %}



### Namen von Basiskomponenten <sup data-p="b">stark empfohlen</sup>

**Basiskomponenten, die applikationsweites Styling und Konventionen hinzuüfgen, sollten alle mit einem bestimmten Präfix beginnen, `Base`, `App` oder `V`.**

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Diese Komponenten legen das Fundament für konsistentes Styling und Verhalten deiner Anwendung. Sie sollten **ausschliesslich** folgendes enthalten:

- HTML-Elemente,
- andere Bassiskomponenten und
- UI-Komponenten von Drittanbietern.

Sie beinhalten **niemals** globalen Status (z.B von Vuex).

Ihre Namen beinhalten oft die Namen der Elemente, die sie umschliessen (z.B. `BaseButton`, `BaseTable`) es sei denn, es existiert kein Element für ein bestimmten Zweck (z.B. `BaseIcon`). Wenn du ähnliche Komponenten für einen spetifischen Zweck baust, werden sie fast immer diese konsumieren (z.B. kann `BaseButton` von `ButtonSubmit` benutzt werden).

Einige Vorteile dieser Konvention:

- Wenn alphabetisch sortiert, werden deine Basiskomponenten alle zusammen aufgelistet und sind einfacher zu finden.

- Da Komponentennamen mehrere Wörter enthalten sollten, verhindert diese Konvention die Not für einen willkürliche Präfix (z.B. `MyButton`, `VueButton`).

- Da diese Komponenten sehr oft benutzt werden, kannst du sie, statt überall zu importieren, mit einem Präfix in Webpack global zugänglich machen:

  ``` js
  var requireComponent = require.context("./src", true, /^Base[A-Z]/)
  requireComponent.keys().forEach(function (fileName) {
    var baseComponentConfig = requireComponent(fileName)
    baseComponentConfig = baseComponentConfig.default || baseComponentConfig
    var baseComponentName = baseComponentConfig.name || (
      fileName
        .replace(/^.+\//, '')
        .replace(/\.\w+$/, '')
    )
    Vue.component(baseComponentName, baseComponentConfig)
  })
  ```

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```
{% raw %}</div>{% endraw %}



### Namen von einmalig instanziierten Komponenten <sup data-p="b">stark empfohlen</sup>

**Komponenten die nur einmal instanziiert werden sollten das Präfix `The` nutzen um deutlich zu machen, dass es nur eine Instanz geben darf.**

Das soll nicht heißen, dass diese Komponenten nur auf einer Seite, sondern dass sie nur einmal _pro Seite_ benutzt werden dürfen. Sie akzeptieren keine Props weil sie applikationsspezifisch sind und nicht vom Kontext innerhalb deiner App abhängen. Solltest du Props brauchen, ist es ein guter Indikator, dass es sich um eine wiederverwendbare Komponente handelt, die _derzeit_ einmal pro Seite verwendet wird.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- Heading.vue
|- MySidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
{% raw %}</div>{% endraw %}



### Eng gekoppelte Komponentennamen <sup data-p="b">stark empfohlen</sup>

**Kindkomponenten die eng gekoppelt sind mit der Elternkomponente sollten den Namen der Elternkomponente als Präfix enthalten.**

Wenn eine Komponente nur innerhalb einer einzigen Elternkomponente sinnvoll ist, dann sollte diese Beziehung im Namen gespiegelt werden. In den meißten Editoren werden Dateien alphabetisch sortiert wodurch diese verwandten Dateien zusammen angezeigt werden.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Du wirst villeicht in Versuchung kommen das Problem mit zusätzlichen Verzeichnissen zu lösen:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

oder:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Das wird nicht empfohlen, weil:

- Viele Dateien mit ähnlichen Namen entstehen und das Wechseln zwischen Dateien in Editoren erschwert wird.
- Viele geschachtelte Verzeichnisse die es erschweren alle Komponenten in der Sidebar eines Editors anzuzeigen.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```
{% raw %}</div>{% endraw %}



### Anordnung der Wörter in Komponentennamen <sup data-p="b">stark empfohlen</sup>

**Komponentennamen sollten mit Wörtern der höchsten Ordnung (d.h. Wörter die am generellsten sind) anfangen und mit möglichst beschreibenden enden.**

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Wenn du dich fragen solltest:

> "Warum sollte ich weniger natürliche Sprache für Komponenten benutzen?"

Im natürlichen Englisch werden Adjektive häufig vor einem Substantiv gestellt und Außnahmen benötigen Hilfswörter:

- Coffee _with_ milk
- Soup _of the_ day
- Visitor _to the_ museum

Du kannst diese Hilfswörter beibehalten wenn du willst, aber die Reihenfolge ist trotzdem wichtig.

Zusätzlich solltest du wissen, dass es **vom Kontext deiner App abhängt welches Wort die höchste Ordnung hat**. Stell dir eine App vor mit einem Suchformular. Es könnte Komponneten wie die folgenden haben:

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Wie dir vielleicht schon auffällt, ist es recht schwer auf ersten Blick zu sehen, welche Komponenten zur Suche gehören. Wenn wir jetzt die Komponenten nach der Regel umbenennen:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Dateien werden meißt alphabetisch in Editoren sortiert. Alle wichtigen Beziehungen sind nun auf ersten Blick erkennbar.

Du kommst vielleicht in Versuchung das Problem mit Verzeichnissen zu lösen. Alle Suchkomponenten in dem Verzeichniss "search" abzulegen, alle Konfigurationskomponenten unter "settings" usw. Wir empfehlen diese Vorgehensweise nur für sehr große Apps (d.h. 100+ Komponenten) aus folgenden Gründen:

- Grundsätzlich bracht man mehr Zeit um verschachtelte Verzeichnisse zu durchsuchen als ein einziges Verzeichniss `components`. 
- Namenskonflikte (z.B. mehrere Komponenten mit dem Namen `ButtonDelete.vue`) erschweren es schnell zu einer bestimmten Komponente zu navigieren in einem Editor.
- Refactoring wird erschwert, weil Find/Replace ist oftmals nicht genug um relative Referenzen zu einer verschobenen Komponenten zu aktualisieren.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```
{% raw %}</div>{% endraw %}



### Selbstschließende Komponenten <sup data-p="b">stark empfohlen</sup>

**Komponenten ohne Inhalt sollten selbstschließend sein in [1-Datei-Komponenten](../guide/single-file-components.html), String-Templates und [JSX](../guide/render-function.html#JSX) - niemals in DOM-Templates.**

Selbstschließende Komponenten kommunizieren nicht nur, dass sie keinen Inhalt haben, sondern, dass sie keinen Inhalt haben **sollen**. Es ist der Unterschied zwischen einer leeren Seite in einm Buch und einer Seite auf der steht "Diese Seite wurde bewusst frei gelassen." Dein Code ist zudem sauberer ohne der unnötigen Tags.

Leider erlaubt HTML keine selbstschließenden benutzerdefinierten Komponenten - nur [offizielle "void"-Elemente](https://www.w3.org/TR/html/syntax.html#void-elements). Deshalb ist diese Strategie nur möglich wenn der Kompiler für Templates diese erreichen kann vor dem DOM und anschließend das korrekte HTML übergibt.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<!-- In single-file components, string templates, and JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- In DOM templates -->
<my-component/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<!-- In single-file components, string templates, and JSX -->
<MyComponent/>
```

``` html
<!-- In DOM templates -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Groß/Keinschreibung in Komponentennamen in Templates <sup data-p="b">stark empfohlen</sup>

**In den meißten Projekten, Komponentennamen sollten PasscalCase folgen in [1-Datei-Komponenten](../guide/single-file-components.html) und String-Templates - Jedoch kebab-case in DOM-Templates.**

PascalCase hat folgende Vorteile gegenüber kebab-case:

- Weil PascalCase auch in JavaScript benutzt wird, können Komponentennamen von Editoren autovervollständigt werden.
- `<MyConponent>` ist visuell einfacher zu unterscheiden von Einwort-Elementen in HTML als `<my-component>`, weil es zwei Unterschiede (zwei Großbuchstaben) statt einem (Bindestrich) gibt.
- Wenn du zusätzliche benutzerdefinierte Elemente in deinen Templates benutzt wie bspw. Web Components, wird  PascalCase sicherstellen, dass du weiterhin Vue-Komponenten gut unterscheiden kannst.

Leider unterscheidet HTML nicht zwischen Groß/Keinschreibung, weshalb DOM-Templates trotzdem kebab-case nutzen müssen.

Wenn du bereits in kebab-case und Konsistenz mit HTML-Konventionen investiert hast, ist vielleicht Konsistenz zwischen all deinen Projekten wichtiger als die o.g. Vorteile. In diesen Fällen ist **der konsistente Gebrauch von kebab-case legitim.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<!-- In single-file components and string templates -->
<mycomponent/>
```

``` html
<!-- In single-file components and string templates -->
<myComponent/>
```

``` html
<!-- In DOM templates -->
<MyComponent></MyComponent>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<!-- In single-file components and string templates -->
<MyComponent/>
```

``` html
<!-- In DOM templates -->
<my-component></my-component>
```

ODER

``` html
<!-- Everywhere -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Groß/Keinschreibung in Komponentennamen in JS/JSX <sup data-p="b">stark empfohlen</sup>

**Komponentennamen in JS/[JSX](../guide/render-function.html#JSX) sollten immer in PascalCase sein obwohl sie kebab-case sein dürfen innerhalb Strings für einfachere Apps, die nur globale Komponentenregistrierung nutzen mit `Vue.component`.**

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

In JavaScript ist PascalCase die Konvention für Klassen und Prototypen - im Grunde alles was instanziiert werden kann. Vue-Komponenten haben ebenfalls Instanzen. Es macht also Sinn ebenfalls PascalCase zu benutzen. Zusätzlich erleichtert der Gebrach von PascalCase innerhalb JSX (und Templates) die Unterscheidung zwischen Komponenten und HTML-Elementen.

Jedoch empfehlen wir kebab-case für Anwendungen, die **ausschließlich** globale Komponenten via `Vue.component` nutzen. Die Gründe sind:

- Es ist selten, dass globale Komponenten in JavaScript referenziert werden, wodurch es weniger sinnvoll ist einer Konvention für JavaScript zu folgen.
- Diese Anwendungen nutzen viele Templates im DOM, wo [kebab-case befolgt werden **muss**](#Component-name-casing-in-templates-strongly-recommended).

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
Vue.component('myComponent', {
  // ...
})
```

``` js
import myComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'myComponent',
  // ...
}
```

``` js
export default {
  name: 'my-component',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
Vue.component('MyComponent', {
  // ...
})
```

``` js
Vue.component('my-component', {
  // ...
})
```

``` js
import MyComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'MyComponent',
  // ...
}
```
{% raw %}</div>{% endraw %}



### Vollwort-Komponentennamen <sup data-p="b">stark empfohlen</sup>

**Komponentennamen sollten immer aus vollen Wörtern und nicht aus Abkürzungen bestehen.**

Autovervollständigung in Editoren halten die Kosten für lange Namen gering. Die gewonnene Klarheit ist unbezahlbar. Vorallem seltene Abkürzungen sollten gemieden werden.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
{% raw %}</div>{% endraw %}



### Groß/Kleinschreibung in Propnamen <sup data-p="b">stark empfohlen</sup>

**Propnamen sollten immer in camelCase deklariert, aber in kebab-case in Templates und in [JSX](../guide/render-function.html#JSX) gebraucht werden.**

Wir folgen einfach den Konventionen der jeweiligen Sprache. In JavaScript, camelCase ist gebräuchlicher und in HTML, kebab-case.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
props: {
  'greeting-text': String
}
```

``` html
<WelcomeMessage greetingText="hi"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
{% raw %}</div>{% endraw %}



### Elemente mit mehreren Attributen <sup data-p="b">stark empfohlen</sup>

**Elemente mit mehreren Attributen sollten mehrere Zeilen einnehmen mit einem Attribut pro Zeile.**

In JavaScript gilt es als gute Konvention Objekte mit mehreren Eigenschaften über mehrere Zeilen zu schreiben, weil es einfacher zu lesen ist. Unsere Templates und [JSX](../guide/render-function.html#JSX) sollten auf gleiche Art berücksichtigt werden.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

``` html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```
{% raw %}</div>{% endraw %}



### Einfache Ausdrücke in Templates <sup data-p="b">stark empfohlen</sup>

**Templates in Komponenten sollten nur einfache Ausdrücke enthalten. Komplexere Ausdrücke sollten in berechnete Eigenschaften oder Methoden umgeschrieben werden.**

Komplexe Ausdrücke machen deine Templates weniger deklarativ. Wir sollten beschreiben _was_ angezeigt wird, nicht _wie_ wir diesen Wert berechnen. Berechnete Eigenschaften und Methoden erlauben außerdem Wiederverwendbarkeit.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<!-- In a template -->
{{ normalizedFullName }}
```

``` js
// The complex expression has been moved to a computed property
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```
{% raw %}</div>{% endraw %}



### Einfache berechnete Eigenschaften <sup data-p="b">stark empfohlen</sup>

**Komplexe berechnete Eigenschaften sollten so weit wie möglich in einfache runtergebrochen werden.**

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Einfachere, gut benannte berechnete Eigenschaften sind:

- __Einfacher zu testen__

  Wenn jede berechnete Eigenschaft nur einfache Ausdrücke mit wenigen Abhängigkeiten enthält ist es wesentlich einfacher dafür Tests zu schreiben um sicherzustellen, dass es korrekt funktioniert.

- __Einfacher zu lesen__

  Wenn du deine berechneten Eigenschaften vereifachst, gibst du jedem Wert ein deskriptiven Namen, selbst wenn es nicht wiederverwendet wird. Damit ist es wesentlich einfacher für andere Entwickler sich einzulesen.

- __Flexibler bezüglich Änderungen__

  Jeder Wert der bennant werden kann, kann im View benutzt werden. Möglicherweise wollen wir dem User anzeigen wie viel Geld er spart. Wir wollen vielleicht zusätzlich die Steuern berechnen, sie aber getrennt anzeigen.
  
  Kleine, gebündelte berechnete Eigenschaften machen weniger Annahmen darüber wie die Informationen benutzt werden, d.h. es muss weniger neu geschrieben werden, wenn sich die Anforderungen ändern.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
computed: {
  price: function () {
    var basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
computed: {
  basePrice: function () {
    return this.manufactureCost / (1 - this.profitMargin)
  },
  discount: function () {
    return this.basePrice * (this.discountPercent || 0)
  },
  finalPrice: function () {
    return this.basePrice - this.discount
  }
}
```
{% raw %}</div>{% endraw %}



### Anfürungszeichen und Attributwerte <sup data-p="b">stark empfohlen</sup>

**Nicht leere HTML-Attributwerte sollten immer in Anführungszeichen sein (einfache oder doppelte, das was nicht in JS benutzt wird).**

Zwar benötigen Werte ohne Leerzeichen keine Anführungszeichen in HTML, führt diese Vorgehensweise dazu, dass Leerzeichen _gemieden_ werden und das macht die Werte weniger lesbar.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
{% raw %}</div>{% endraw %}



### Kurzschrift für Direktive <sup data-p="b">stark empfohlen</sup>

**Die Kurzschrift für Direktive (`:` für `v-bind:` und `@` für `v-on:`) sollte entweder immer oder nie benutzt werden.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

``` html
<input
  @input="onInput"
  @focus="onFocus"
>
```

``` html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```
{% raw %}</div>{% endraw %}




## Priorität C Regeln: Empfohlen (Minimalisierung von Willkür und kognitivem Aufwand)



### Abfolgen von Optionen in Komponenten/Instanzen <sup data-p="c">Empfohlen</sup>

**Optionen in Komponenten/Instanzen sollten konsistent geordnet sein.**

Hier ist die Standardabfolge, die wir für Komponentenoptionen empfehlen. Sie sind in Kategorien aufgeteilt, sodass es klar sein sollte, wo du neue Eigenschaften von Plugins hinzufügen kannst.

1. **Seiteneffekte** (Bewirkt Effekte außerhalb der Komponente)
  - `el`

2. **Globale Bekanntheit** (Erfordert Wissen außerhalb der Komponente)
  - `name`
  - `parent`

3. **Komponententyp** (Ändert der Typ der Komponente)
  - `functional`

4. **Template-Modifizierer** (Ändert wie Template kompiliert werden)
  - `delimiters`
  - `comments`

5. **Template-Abhängigkeiten** (Gebrauchte Assets im Template)
  - `components`
  - `directives`
  - `filters`

6. **Komposition** (Hinzufügen von Eigenschaften)
  - `extends`
  - `mixins`

7. **Interface** (Das Interface für die Komponente)
  - `inheritAttrs`
  - `model`
  - `props`/`propsData`

8. **Lokaler Status** (Lokale reaktive Eigenschaften)
  - `data`
  - `computed`

9. **Events** (Durch reaktive Events ausgelöste Callbacks)
  - `watch`
  - Lifecycle-Events (In der Abfoge in der sie aufgerufen werden)
    - `beforeCreate`
    - `created`
    - `beforeMount`
    - `mounted`
    - `beforeUpdate`
    - `updated`
    - `activated`
    - `deactivated`
    - `beforeDestroy`
    - `destroyed`

10. **Nicht reaktive Eigenschaften** (Instanzeigenschaften unabhängig von dem Reaktivitätssystem)
  - `methods`

11. **Rendering** (Die deklarative Beschreibung vom Output der Komponente)
  - `template`/`render`
  - `renderError`



### Abfolge der Elementattribute <sup data-p="c">Empfohlen</sup>

**Die Elementenattribute (inkl. Komponenten) sollten konsistent geordnet sein.**

Hier ist die Standardabfolge, die wir für Komponentenoptionen empfehlen. Sie sind in Kategorien aufgeteilt, sodass es klar sein sollte, wo du benutzerdefinierte Attribute und Direktive hinzufügen kannst.

1. **Definition** (Bereitstellen von Komponentenoptionen)
  - `is`

2. **Rendering von Listen** (Erstellen ovn mehreren Variationen vom selben Element)
  - `v-for`

3. **Bedingungen** (Ob das Element gerendert/gezeigt werden soll)
  - `v-if`
  - `v-else-if`
  - `v-else`
  - `v-show`
  - `v-cloak`

4. **Render-Modifizierer** (Ändert wie das Element gerendert wird)
  - `v-pre`
  - `v-once`

5. **Globale Bekanntheit** (Erfordert Wissen außerhalb der Komponente)
  - `id`

6. **Einmalige Attribute** (Attribute mit einmaligen Werten)
  - `ref`
  - `key`
  - `slot`

7. **Zweiwegverknüpfung** (Kombiniert Veknüpfung und Events)
  - `v-model`

8. **Andere Attribute** (Alle nicht spezifizierten gebundene und nicht gebundene Attribute)

9. **Events** (Event-Listener für Komponenten)
  - `v-on`

10. **Inhalt** (Überschreibt den Inhalt von Elementen)
  - `v-html`
  - `v-text`



### Leerzeilen in Optionen von Komponenten/Instanzen <sup data-p="c">Empfohlen</sup>

**Du solltest zwischen Eigenschaften, die mehrere Zeilen einnehmen, eine Zeile frei lassen, insbesondere wenn die Optionen nicht länger auf eine Bildschirmseite passen.**

Wenn Komponenten anfagen überfüllt zu wirken, können Leerzeilen dabei helfen es besser lesbar zu machen. In manchen Editoren, bspw. Vim, kann diese Formatierung die Navigation zwischcen Optionen mit der Tastatur vereinfachen.

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue: function () {
    // ...
  },

  inputClasses: function () {
    // ...
  }
}
```

``` js
// No spaces are also fine, as long as the component
// is still easy to read and navigate.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue: function () {
    // ...
  },
  inputClasses: function () {
    // ...
  }
}
```
{% raw %}</div>{% endraw %}



### Top-Level Elementenordnung in 1-Datei-Komponenten <sup data-p="c">Empfohlen</sup>

**[1-Datei-Komponenten](../guide/single-file-components.html) sollten immer die Tags `<script>`, `<template>`, und `<style>` konsistent anordnen mit `<style>` an letzter Stelle, weil eins der anderen immer notwendig ist.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

``` html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}



## Priorität D Regeln: Auf eigene Gefahr (Potentiell gefährliche Muster)



### `v-if`/`v-if-else`/`v-else` ohne `key` <sup data-p="d">Auf eigene Gefahr</sup>

**Es ist normalerweise eine gute Idee `key` mit `v-if` + `v-else` zu benutzen, wenn sie vom gleichen Typ sind (z.B. wenn beide Tags `<div>` sind).**

Per Default versucht Vue so effizient wie möglich das DOM zu aktualisieren. Deshalb wird beim Wechsel zwischen Elementen vom gleichen Typ einfach das existierende Element aktualisert, statt es zu löschen und ein neues hinzuzufügen. Wenn diese Elemente nicht wirklich gleich sind, kann das zu [ungewollten Nebenwirkungen](https://jsfiddle.net/chrisvfritz/bh8fLeds/) führen.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<div v-if="error">
  Error: {{ error }}
</div>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<div
  v-if="error"
  key="search-status"
>
  Error: {{ error }}
</div>
<div
  v-else
  key="search-results"
>
  {{ results }}
</div>
```

``` html
<p v-if="error">
  Error: {{ error }}
</p>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}



### Elementselektoren mit `scoped` <sup data-p="d">Auf eigene Gefahr</sup>

**Elementselektoren sollten mit `scoped` gemieden werden.**

Bevorzuge Klassenselektoren in Styles mit `scoped`, weil zu viele Elementselektoren langsam sind.

{% raw %}
<details>
<summary>
  <h4>Weiterführende Erklärung</h4>
</summary>
{% endraw %}

Es wird ein einmaliges Attribut zu Komponentenelementen hinzugefügt, bspw. `data-v-f3f3eg9`, um `scoped` in Vue umzusetzen. Danach werden Selektoren modifiziert, sodass nur zutreffende Elemente ausgewählt werden (z.B. `button[data-v-f3f3eg9]`).

Das Problem ist, dass eine große Anzahl von [Elementattributselektoren](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (z.B. `button[data-v-f3f3eg9]`) wesentlich langsamer sein werden als [Klassenattributselektoren](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000), (z.B. `.btn-close[data-v-f3f3eg9]`), d.h. Klassenselektoren sollten bevorzugt werden.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` html
<template>
  <button>X</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Implizierte Eltern-Kind Kommunikation <sup data-p="d">Auf eigene Gefahr</sup>

**Props und Events sollten bevorzugt werden für Eltern-Kind Komponentenkommunikation, statt `this.$parent` und Props zu verändern.**

Die Kommunikation einer idealen App in Vue ist Props-Down und Events-Up. Wenn du dich an diese Konvention hällst werden deine Komponenten wesentlich einfacher nachzuvollziehn sein. Jedoch in manchen Grenzfällen kann `this.$parent` und das Abändern von Props zwei Komponenten vereinfachen, die sowieso schon eng gekoppelt sind.

Das Problem ist, dass es auch viele _einfache_ Fälle gibt in denen diese Muster Bequemlichkeit schaffen. Achtung: Du solltest nicht Einfachheit (Nachvollziehbarkeit vom Status) für kurzzeitige Bequemlichkeit (Weniger Code schreiben) aufgeben.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: '<input v-model="todo.text">'
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: {
    removeTodo () {
      var vm = this
      vm.$parent.todos = vm.$parent.todos.filter(function (todo) {
        return todo.id !== vm.todo.id
      })
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}



### Nicht-flux Statusmanagement <sup data-p="d">Auf eigene Gefahr</sup>

**[Vuex](https://github.com/vuejs/vuex) sollte bevorzugt werden für globales Statusmanagement, statt `this.$root` oder einem globalen Eventbus.**

Den Status via `this.$root` und/oder einem [globalen Eventbus](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced) zu verwalten, kann für sehr einfache Fälle bequem sein, ist aber für die meisten Apps nicht angemessen. Vuex bietet nicht nur eine zentrale Stelle um Status zu verwalten, aber auch Werkzeug zum Organisieren, Verfolgen und Debugging von Statusänderungen.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Schlecht

``` js
// main.js
new Vue({
  data: {
    todos: []
  },
  created: function () {
    this.$on('remove-todo', this.removeTodo)
  },
  methods: {
    removeTodo: function (todo) {
      var todoIdToRemove = todo.id
      this.todos = this.todos.filter(function (todo) {
        return todo.id !== todoIdToRemove
      })
    }
  }
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Gut

``` js
// store/modules/todos.js
export default {
  state: {
    list: []
  },
  mutations: {
    REMOVE_TODO (state, todoId) {
      state.list = state.list.filter(todo => todo.id !== todoId)
    }
  },
  actions: {
    removeTodo ({ commit, state }, todo) {
      commit('REMOVE_TODO', todo.id)
    }
  }
}
```

``` html
<!-- TodoItem.vue -->
<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo(todo)">
      X
    </button>
  </span>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: mapActions(['removeTodo'])
}
</script>
```
{% raw %}</div>{% endraw %}



{% raw %}
<script>
(function () {
  var enforcementTypes = {
    none: '<span title="There is unfortunately no way to automatically enforce this rule.">self-discipline</span>',
    runtime: 'runtime error',
    linter: '<a href="https://github.com/vuejs/eslint-plugin-vue#eslint-plugin-vue" target="_blank">plugin:vue/recommended</a>'
  }
  Vue.component('sg-enforcement', {
    template: '\
      <span>\
        <strong>Enforcement</strong>:\
        <span class="style-rule-tag" v-html="humanType"/>\
      </span>\
    ',
    props: {
      type: {
        type: String,
        required: true,
        validate: function (value) {
          Object.keys(enforcementTypes).indexOf(value) !== -1
        }
      }
    },
    computed: {
      humanType: function () {
        return enforcementTypes[this.type]
      }
    }
  })

  // new Vue({
  //  el: '#main'
  // })
})()
</script>
{% endraw %}
