---
title: TypeScript Support
type: guide
order: 404
---

> Mit Vue 2.5.0+ haben wir die Typdeklaration dahingehend überarbeitet, dass es mit der vorgegebenen objektbasierten API funktioniert. Gleichzeitig werden einige Änderungen eingeführt, welche ein Upgrade benötigen. Lies [diesen Blog](https://medium.com/the-vue-point/upcoming-typescript-changes-in-vue-2-5-e9bd7e2ecf08) für weitere Details.

## Offizielle Deklaration in NPM-Paketen

Ein statisches Typsystem kann helfen viele potentielle Fehler, besonders während die Anwendung wächst, zu vermeiden. Aus diesem Grund kommt Vue mit [offiziellen Typdeklarationen](https://github.com/vuejs/vue/tree/dev/types) für [TypeScript](https://www.typescriptlang.org/) - Nicht nur für das Vue-Kernprodukt, sondern auch für [vue-router](https://github.com/vuejs/vue-router/tree/dev/types) und [vuex](https://github.com/vuejs/vuex/tree/dev/types).

Diese werden auf [NPM veröffenticht](https://cdn.jsdelivr.net/npm/vue/types/) und das aktuelle TypeScipt kann Typdeklarationen in NPM-Paketen auflösen, d.h. wenn mit NPM installiert, brauchst du kein zusätzliches Werkzeug um TypeScript mit Vue zu nutzen.

## Empfohlene Konfiguration

``` js
// tsconfig.json
{
  "compilerOptions": {
    // das passt sich Vue's Browsersupport an
    "target": "es5",
    // das aktiviert strengere Inferenz für Dateneigenschaften auf `this`
    "strict": true,
    // mit Webpack 2+ oder Rollup, um Tree-Shaking zu hebeln:
    "module": "es2015",
    "moduleResolution": "node"
  }
}
```

Beachte, dass du `strict: true` einfügen musst (oder wenigstens `noImplicitThis: true` was Teil von `strict` ist) um Typprüfung auf `this` in Komponentenmethoden zu hebeln, andernfalls wird es immer als Typ `any` behandelt.

Siehe [TypeScript Compileroptionen](https://www.typescriptlang.org/docs/handbook/compiler-options.html) für weitere Details.

## Entwicklungswerkzeug

### Projekterstellung

[Vue CLI 3](https://github.com/vuejs/vue-cli) kann neue Projekte, die TypeScript nutzen, generieren. Um anzufangen:

```bash
# 1. Installiere Vue CLI, wenn es nicht bereitst installiert ist
npm install --global @vue/cli

# 2. Erstell ein neues Projekt, danach wähle die Option "Manually select features"
vue create my-project-name
```

### Editorsupport

Zum Entwickeln von Vue-Anwendungen mit TypeScript empfehlen wir [Visual Studio Code](https://code.visualstudio.com/) da es TypeScript besonders gut unterstützt. Wenn du [1-Datai-Komponenten](./single-file-components.html) benutzt, solltest du die Extension [Vetur](https://github.com/vuejs/vetur) installieren, diese bietet TypeScript-Inferenzinnerhalb der Komponenten und viele andere tolle Features.

[WebStorm](https://www.jetbrains.com/webstorm/) bietet ebenfalls vorkonfigurierten Support für TypeScript und Vue.

## Grundlegende Handhabung 

Damit TypeScript Datentypen innerhalb der Komponentenoptionen richtig inferieren kann musst du deine Komponenten mit `Vue.component` oder `Vue.extend` definieren:

``` ts
import Vue from 'vue'

const Component = Vue.extend({
  // Typinferenz aktiviert
})

const Component = {
  // Das wird KEINE Typinfenez haben,
  // weil TypeScript es nicht als Optionen einer Komponente erkennt.
}
```

## Klassenbasierte Komponenten

Wenn du eine klassenbasierte API beim Deklarieren von Komponenten bevorzugst, kannst du den offiziellen Decorator [vue-class-component](https://github.com/vuejs/vue-class-component) nutzen:

``` ts
import Vue from 'vue'
import Component from 'vue-class-component'

// Der Decorator @Component zeigt an, dass die Klasse eine Vue-Komponente ist
@Component({
  // Alle Komponentenoptionen sind hier erlaubt
  template: '<button @click="onClick">Click!</button>'
})
export default class MyComponent extends Vue {
  // Initialdaten können als Instanzeigenschaften deklariert werden
  message: string = 'Hello!'

  // Komponentenmethoden können als Instanzmethoden deklariert werden
  onClick (): void {
    window.alert(this.message)
  }
}
```

## Erweitere Datentypen in Plugins

Plugins können globale sowie Instanzeigenschaften und Komponentenoptionen hinzufügen. In diesen Fällen, Typdeklarationen werden benötigt um Plugins in TypeScript zu kompilieren. Glücklicherweise gibt es das Modul [augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation) in TypeScript um existierende Datentypen zu erweitern.

Zum Beispiel, um eine Instanzeigenschaft `$myProperty` mit dem Datentyp `string` zu deklarieren:

``` ts
// 1. Importieren 'vue' bevor du erweiterte Datentypen deklarierst
import Vue from 'vue'

// 2. Gib eine Datei mit den Datentypen, die du erweitern willst, an
//    Vue hat die Konstruktortypen in types/vue.d.ts
declare module 'vue/types/vue' {
  // 3. Deklariere die Erweiterung für Vue
  interface Vue {
    $myProperty: string
  }
}
```

Nachdem du o.g. Code als Deklarationsdatei (bspw. `my-property.d.ts`) in deinem Projekt hinzufügst, kannst du `$myProperty` auf einer Instanz nutzen.

```ts
var vm = new Vue()
console.log(vm.$myProperty) // Das sollte erfolgreich kompilieren
```

Du kannst auch zusätzliche globale Eigenschaften und Komponentenoptionen deklarieren:

```ts
import Vue from 'vue'

declare module 'vue/types/vue' {
  // Globale Eigenschaften können auf
  // `VueConstructor` deklariert werden
  interface VueConstructor {
    $myGlobal: string
  }
}

// ComponentOptions ist in types/options.d.ts deklariert
declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    myOption?: string
  }
}
```

Die o.g. Deklarationen erlauben es folgenden Code zu kompilieren:

```ts
// Globale Eigenschaft
console.log(Vue.$myGlobal)

// Zusätzliche Komponentenoption
var vm = new Vue({
  myOption: 'Hello'
})
```

## Rückgabewerte anmerken

Aufgrund der kreisförmigen Natur der Deklarationsdateien in Vue, kann TypeScript Schwierigkeiten haben die Datentypen von bestimmten Methoden zu inferieren. Aus diesem Grund musst du die Rückgabewerte der Methoden wie `render` und diejenigen in `computed` anmerken.

```ts
import Vue, { VNode } from 'vue'

const Component = Vue.extend({
  data () {
    return {
      msg: 'Hello'
    }
  },
  methods: {
    // Braucht Anmerkung weil `this` im Rückgabewert benutzt wird
    greet (): string {
      return this.msg + ' world'
    }
  },
  computed: {
    // Braucht Anmerkung
    greeting(): string {
      return this.greet() + '!'
    }
  },
  // `createElement` wird inferiert, aber `render` braucht ein Rückgabetyp
  render (createElement): VNode {
    return createElement('div', this.greeting)
  }
})
```
Wenn Typinferenz oder Mitgliedervervollständigung nicht funktioniert, kann es helfen bestimmte Methoden anzumerken. Die Option `--noImplicitAny` kann dabei helfen viele dieser Methoden zu finden.
