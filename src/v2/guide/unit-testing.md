---
title: Unit-Testing
type: guide
order: 403
---

## Setup and Werkzeug

Alles was mit einem modulbasierten Build-System kompatibel ist, wird funktionieren, aber wenn du eine spezielle Emfehung willst, solltest du [Karma](http://karma-runner.github.io) ausprobieren. Es hat viele Plugins aus der Community, inkl. Support für [Webpack](https://github.com/webpack/karma-webpack) und [Browserify](https://github.com/Nikku/karma-browserify). Die Details bezüglich Setup kannst du der Dokumentation der jeweiligen Projekte entnehmen. Die folgenden Konfigurationsbeispiel für [Webpack](https://github.com/vuejs-templates/webpack/blob/master/template/test/unit/karma.conf.js) und [Browserify](https://github.com/vuejs-templates/browserify/blob/master/template/karma.conf.js) können dir dabei helfen.

## Einfache Behauptungen

Du brauchst nichts extra innerhalb deiner Komponenten hinzuzufügen um sie testbar zu machen. Exportiere die rohen Optionen:

``` html
<template>
  <span>{{ message }}</span>
</template>

<script>
  export default {
    data () {
      return {
        message: 'hello!'
      }
    },
    created () {
      this.message = 'bye!'
    }
  }
</script>
```

Danach importiere die Komponentenoptionen mit Vue und du kannst viele gängige Behauptungen aufstellen:

``` js
// Importiere Vue und die zu testende Komponente
import Vue from 'vue'
import MyComponent from 'path/to/MyComponent.vue'

// Hier sind einige Jasmine 2.0 Tests, aber du kannst
// jeden Test-Runner / Assertion-Library verwenden
describe('MyComponent', () => {
  // Inpiziere die rohen Komponentenoptionen
  it('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Evaluiere das Egebnis der Funktion in
  // den rohen Komponentenoptionen
  it('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Inspiziere die Komponenteninstanz beim einhängen
  it('correctly sets the message when created', () => {
    const vm = new Vue(MyComponent).$mount()
    expect(vm.message).toBe('bye!')
  })

  // Hänge die Instanz ein und inspiziere die gerenderte Ausgabe
  it('renders the correct message', () => {
    const Constructor = Vue.extend(MyComponent)
    const vm = new Constructor().$mount()
    expect(vm.$el.textContent).toBe('bye!')
  })
})
```

## Testbare Komponenten schreiben

Die Ausgabe einer Komponente ist abhängig von ihren Props. Wenn die Ausgabe ausschließlich von den Props abhängt ist es einfach zu testen, ähnlich einer reinen Funktion mit verschiedenen Argumenten. Zum Beispiel:

``` html
<template>
  <p>{{ msg }}</p>
</template>

<script>
  export default {
    props: ['msg']
  }
</script>
```

Du kannst Behauptungen für die Ausgabe aufstellen, abh. von den Props mit der Option `propsData`:

``` js
import Vue from 'vue'
import MyComponent from './MyComponent.vue'

// Helfer-Funtion, die den gerenderten Text einhängt und zurückgibt
function getRenderedText (Component, propsData) {
  const Constructor = Vue.extend(Component)
  const vm = new Constructor({ propsData: propsData }).$mount()
  return vm.$el.textContent
}

describe('MyComponent', () => {
  it('renders correctly with different props', () => {
    expect(getRenderedText(MyComponent, {
      msg: 'Hello'
    })).toBe('Hello')

    expect(getRenderedText(MyComponent, {
      msg: 'Bye'
    })).toBe('Bye')
  })
})
```

## Behauptungen aufstellen für asynchrone Updates



Da Vue [DOM-Updates asynchron ausführt](reactivity.html#Async-Update-Queue), müssen Behauptungen bezüglich DOM-Updates basierend auf Statusänderungen mit dem Callback `Vue.nextTick` gemacht werden:

``` js
// Inspiziere das generierte HTML nach einer Statusänderung
it('updates the rendered message when vm.message updates', done => {
  const vm = new Vue(MyComponent).$mount()
  vm.message = 'foo'

  // Warte ein "tick" nach Statusänderungen vor dem Prüfen vom DOM-Updates 
  Vue.nextTick(() => {
    expect(vm.$el.textContent).toBe('foo')
    done()
  })
})
```

Wir planen eine Sammlung von geläufigen Helfern für Tests um es einfacher zu machen Komponenten mit verschiedenen Beschränkungen zu rendern (d.h. flaches Rendern, dass Kind-Komponenten ignoriert) und die Ausgabe zu prüfen.

Für Details zu Unit-Testing in Vue, schau dir [vue-test-utils](https://vue-test-utils.vuejs.org/) und den Eintrag in unserem Kochbuch zu [Unit-Testing von Vue-Komponenten](https://vuejs.org/v2/cookbook/unit-testing-vue-components.html) an.
