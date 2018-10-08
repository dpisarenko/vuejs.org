---
title: Erstellen von Unittests für Vue-Komponenten
type: cookbook
order: 6
---

## Grundlegendes Beispiel

Unittests sind ein fundamentaler Bestandteil der Softwareentwicklung. Unittests führen die kleinsten Einheiten des Codes unabhängig von anderen Teilen. Dies vereinfacht das Hinzufügen neuer Funktionalitäten und das Finden von Fehlern. Die [1-Datei-Komponenten](../guide/single-file-components.html) von Vue machen es einfach, Komponenten mit Unittests isoliert von anderen Codeteilen zu testen. Das erlaubt Dir, neue Funktionalitäten hinzuzufügen und dabei sicher zu sein, dass Du die alten nicht beschädigst. Außerdem hilft das anderen Entwicklern, Deinen Code zu verstehen.

Dieser einfache Beispieltest überprüft, ob ein bestimmter Text dargestellt wird:

```html
<template>
  <div>
    <input v-model="username">
    <div 
      v-if="error"
      class="error"
    >
      {{ error }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'Hello',
  data () {
    return {
      username: ''
    }
  },

  computed: {
    error () {
      return this.username.trim().length < 7
        ? 'Gebe bitte einen längeren Benutzernamen ein'
        : ''
    }
  }
}
</script>
```

```js
import { shallowMount } from '@vue/test-utils'

test('Foo', () => {
  // Rendere die Komponente
  const wrapper = shallowMount(Hello)

  // Benutzernamen (`username`) mit weniger als 7 Zeichen, ausgenommen Leerzeichen, sind nicht erlaubt
  wrapper.setData({ username: ' '.repeat(7) })

  // Behaupte, dass der Fehler dargestellt wird
  expect(wrapper.find('.error').exists()).toBe(true)

  // Verändere den Namen sodass er lang genug wird
  wrapper.setData({
    username: 'Lachlan'
  })

  // Behaupte, dass der Fehler verschwunden ist
  expect(wrapper.find('.error').exists()).toBe(false)
})
```

Das obige Code-Fragment zeigt, wie man testen kann, ob eine Fehlermeldung dargestellt wird, je nach der Länge des Benutzernamen. Es demonstriert die allgemeine Idee der Unittests für Vue-Komponenten: rendere die Komponente und behaupte, dass der HTML-Code dem Zustand der Komponente entspricht.

## Warum Tests?

Komponenten-Unittests bieten eine Menge Vorteile:

- Sie dokumentieren, wie sich eine Komponente verhalten soll
- Sie sparen Zeit im Vergleich zu manuellen Tests
- Verringern die Anzahl der Fehler in neuen Funktionalitäten
- Verbessern den Entwurf (Design) der Anwendung
- Ermöglichen das Refaktorisieren

Automatische Tests erlauben grossen Teams, komplexe Codebases zu warten.

#### Erste Schritte

[Vue Test Utils](https://github.com/vuejs/vue-test-utils) ist die offizielle Bibliothek für das Unittesten der Vue-Komponenten. Die Vorlate `webpack` aus [vue-cli](https://github.com/vuejs/vue-cli) wird entweder mit Karma oder Jest ausgeliefert. Diese beiden Testausführer sind gut unterstützt und es gibt einige [Anleitungen](https://vue-test-utils.vuejs.org/guides/) in der Dokumentation von Vue Test Utils.

## Beispiel aus der realen Welt

Unittest sollten

- Schnell ausgeführt werden können
- Leicht zu verstehen sein
- Ein Test sollte immer nur _eine einzige Arbeitseinheit_ testen

Setzen wir das letzte Beispiel fort. Wir werden das Konzept einer [Fabrikmethode](https://de.wikipedia.org/wiki/Fabrikmethode) einführen, damit unser Test kompakter und lesbarer wird. Die Komponente sollte:

- Den Gruss 'Willkommen im Vue.js-Kochbuch' anzeigen.
- Den Benutzer nach seinem Benutzernamen fragen.
- Einen Fehler anzeigen, wenn der eingegebene Benutzername kürzer als sieben Buchstaben ist.

Sehen wir uns zuerst den Code der Komponente an:

```html
<template>
  <div>
    <div class="message">
      {{ message }}
    </div>
    Gebe bitte Deinen Benutzernamen ein: <input v-model="username">
    <div 
      v-if="error"
      class="error"
    >
      Gebe bitte einen Benutzernamen mit mindestens 7 Buchstaben ein.
    </div>
  </div>
</template>

<script>
export default {
  name: 'Foo',

  data () {
    return {
      message: 'Willkommen im Vue.js-Kochbuch',
      username: ''
    }
  },

  computed: {
    error () {
      return this.username.trim().length < 7
    }
  }
}
</script>
```

Wir sollten folgende Dinge testen:

- wird `message` dargestellt?
- Wenn `error` wahr ist, dann sollte `<div class="error">` vorhanden sein
- Wenn `error` falsch ist, sollte `<div class="error">` nicht vorhanden sein

Unser erster Versuch, einen Test zu schreiben:

```js
import { shallowMount } from '@vue/test-utils'

describe('Foo', () => {
  it('Rendert die Nachricht und reagiert korrekt auf die Benutzereingabe', () => {
    const wrapper = shallowMount(Foo, {
      data: {
        message: 'Hello World',
        username: ''
      }
    })

    // Prüfe, ob die Nachricht gerendert wird
    expect(wrapper.find('.message').text()).toEqual('Hello World')

	// Behaupte, dass der Fehler dargestellt wird
    expect(wrapper.find('.error').exists()).toBeTruthy()
	
	// Verändere den Benutzernamen (`username`) und behaupte, dass der Fehler nicht mehr dargestellt wird
    wrapper.setData({ username: 'Lachlan' })
    expect(wrapper.find('.error').exists()).toBeFalsy()
  })
})
```

Der obige Code hat mehrere Probleme:

- Ein Test macht Behauptungen über unterschiedliche Dinge
- Es ist schwer festzustellen, in welchen Zuständen sich die Komponente befinden kann, und das gerendert werden soll

Das Beispiel unten verbessert den Test dadurch, dass

- nur eine Behauptung pro `it`-Block gemacht wird,
- die Testbeschreibungen kurz und klar sind,
- nur die für den Test minimal erforderlichen Daten angeführt werden,
- die duplizierte Logik (Erstellen von `wrapper` und Setzen der Variable `username`) in eine Fabrikfunktion ausgelagert wird.

*Aktualisierter Test*:
```js
import { shallowMount } from '@vue/test-utils'
import Foo from './Foo'

const factory = (values = {}) => {
  return shallowMount(Foo, {
    data: { ...values  }
  })
}

describe('Foo', () => {
  it('Rendert eine Willkommensnachricht', () => {
    const wrapper = factory()

    expect(wrapper.find('.message').text()).toEqual("Willkommen zum Vue.js-Kochbuch")
  })

  it('Rendert einen Fehler, wenn der Benutzername kürzer als 7 Zeichen ist', () => {
    const wrapper = factory({ username: ''  })

    expect(wrapper.find('.error').exists()).toBeTruthy()
  })

  it('Rendert einen Fehler, wenn der Benutzername nur aus Leerzeichen besteht', () => {
    const wrapper = factory({ username: ' '.repeat(7) })

    expect(wrapper.find('.error').exists()).toBeTruthy()
  })

  it('Rendert keinen Fehler, wenn der Benutzername 7 Zeichen lang oder länger ist', () => {
    const wrapper = factory({ username: 'Lachlan'  })

    expect(wrapper.find('.error').exists()).toBeFalsy()
  })
})
```

Punkte, die Du merken kannst:

Oben deklarieren wir eine Fabrikfunktion, die den `values`-Objekt in `data` überträgt und eine neue `wrapper`-Instanz zurückgibt. Damit brauchen wir `const wrapper = shallowMount(Foo)` nicht in jedem Test zu duplizieren. Ein anderer grosser Vorteil besteht darin, dass wenn Du eine Methode oder berechnete Eigenschaft in jedem Test mocken oder stubben willst, Du dies nur ein Mal tun musst.

## Zusätzlicher Kontext

Der obige Test ist relativ einfach, aber in der Praxis haben Vue-Komponenten oft andere Verhalten, die Du testen willst, z. B.

- API-Aufrufe machen
- Veränderungen oder Aktionen mit `Vuex` versenden oder committen
- Interaktionen testen

In den [Anleitungen](https://vue-test-utils.vuejs.org/guides/) zu Vue Test Utils gibt es ausführlichere Beispiele, die zeigen, wie man solche Test macht.

Vue Test Utils und das riesige JavaScript-Ökosystem stellt eine Menge Werkzeuge zur Verfügung, die eine Testabdeckung von beinahe 100% ermöglichen. Unittests sind jedoch nur ein Teil der Testpyramide. Es gibt auch E2E- (end to end) und Snapschuss-Tests. Unittests sind die kleinsten und einfachsten Tests - sie machen Behauptungen bezüglich kleinster Codeeinheiten. Dadurch wird jeder Teil einer Komponente isoliert.

Snapschuss-Tests speichern den HTML-Code Deiner Komponente und vergleichen ihn mit dem neugenerierten bei jedem Testdurchlauf. Wenn sich etwas verändert, wird der Entwickler benachrigt und kann dann entscheiden, ob die Änderung absichtlich (die Komponente wurde verändert) oder versehentlich (die Komponente verhält sich falsch) war.

End-to-end-Tests stellen sicher, dass mehrere Komponenten miteinander richtig interagieren. Sie befinden sich auf einer höheren Ebene. Ein Beispiel dafür wäre ein Test, der überprüft, ob sich ein Benutzer registrieren, einloggen, und danach seinen Benutzernamen verändern kann. Solche Tests sind langsamer als Unit- oder Schnapschuss-Tests.

Unittests sind während der Entwicklung am nützlichsten. Sie helfen einem Entwickler zu verstehen, wie eine Komponente entworfen werden soll, oder eine bestehende Komponente zu refaktorisieren. Oft werden sie nach jeder Codeänderung ausgeführt.

Tests auf höheren Ebenen, wie die End-to-End-Tests, brauchen wesentlich mehr Zeit zum Laufen. Sie werden häufig vor dem Installieren einer neuen Software-Version ausgeführt, um sicherzustellen, dass alle Teile miteinander richtig interagieren.

Mehr Informationen über das Testen von Vue-Komponenten gibt es im Buch [Testing Vue.js Applications](https://www.manning.com/books/testing-vuejs-applications) vom Kern-Team-Mitglied [Edd Yerburgh](https://eddyerburgh.me/), 

## Wann dieses Muster vermieden werden soll

Unittests sind ein wichtiger Teil jeder ernsthaften Anwendung. Am Beginn, wenn die Vision der Anwendung unklar ist, können Unittests die Entwicklung bremsen. Sobald aber die Vision erkannt ist und reale Benutzer mit der Anwendung arbeiten, sind Unittests (und andere automatischen Tests) absolut unentbehrlich, um die Codebasis in einem wartbaren und skalierbaren Zustand zu halten.