---
title: Memory-Leaks vermeiden
type: cookbook
order: 10
---

## Einführung

Wenn Sie Anwendungen mit Vue entwickeln, müssen Sie auf Memory-Leaks aufpassen. Dies ist bei Single Page Applications (SPAs) besonders wichtig. Aufgrund ihres Designs sollten die Benutzer die Seite nicht neuladen müssen. Daher liegt es in der Verantwortung der JavaScript-Anwendung, Komponenten freizugeben und sicherzustellen, dass Garbage Collection wie erwartet durchgeführt wird.

Die meisten Memory Leaks werden nicht durch Vue selber verursacht, sondern passieren, wenn Fremdbibliotheken in eine Anwendung importiert werden. 

## Ein einfaches Beispiel

Das folgende Beispiel zeigt ein Memory leak, welches durch die Verwendung der [Choices.js](https://github.com/jshjohnson/Choices)-Bibliothek ohne entsprechende Aufräumarbeiten verursacht wurde. Später zeigen wir, wie das Memory Leak behoben werden kann.

Im Beispiel unten laden wir eine Auswahlliste mit vielen Optionen hoch. Dann verwenden wir eine Schaltfläche, die mit der [v-if](/v2/guide/conditional.html)-Direktive zum virtuellen DOM hinzugefügt und von dort entfernt werden kann. Das Problem mit diesem Beispiel ist, dass die `v-if`-Direktive das Elternelement aus dem DOM entfernt, jedoch bereinigen wir nicht die zusätzlichen DOM-Teile, die durch Choices.js erstellt wurden. Das ist die Ursache des Memory Leaks.

```html
<link rel='stylesheet prefetch' href='https://joshuajohnson.co.uk/Choices/assets/styles/css/choices.min.css?version=3.0.3'>
<script src='https://joshuajohnson.co.uk/Choices/assets/scripts/dist/choices.min.js?version=3.0.3'></script>

<div id="app">
  <button
    v-if="showChoices"
    @click="hide"
  >Hide</button>
  <button
    v-if="!showChoices"
    @click="show"
  >Show</button>
  <div v-if="showChoices">
    <select id="choices-single-default"></select>
  </div>
</div>
```

```js
new Vue({
  el: "#app",
  data: function () {
    return {
      showChoices: true
    }
  },
  mounted: function () {
    this.initializeChoices()
  },
  methods: {
    initializeChoices: function () {
      let list = []
      // Hier wollen wir unsere Select-Komponente mit
      // vielen Auswahloptionen hochladen, damit es
      // eine Menge Speicher konsumiert.
      for (let i = 0; i < 1000; i++) {
        list.push({
          label: "Item " + i,
          value: i
        })
      }
      new Choices("#choices-single-default", {
        searchEnabled: true,
        removeItemButton: true,
        choices: list
      })
    },
    show: function () {
      this.showChoices = true
      this.$nextTick(() => {
        this.initializeChoices()
      })
    },
    hide: function () {
      this.showChoices = false
    }
  }
})
```

Um dieses Memory Leak in Aktion zu sehen, öffnen Sie dieses [CodePen-Beispiel](https://codepen.io/freeman-g/pen/qobpxo) in Chrome. Öffnen Sie anschließend den Chrome Task Manager. Um den Chrome Task Manager am Mac zu öffnen, wählen Sie Chrome Top Navitation > Window > Task Manager. Unter Windows können Sie den Tastaturkürzel Shift+Esc nutzen. Klicken Sie nun die Schaltfläche "Show"/"Hie" ungefähr 50 Mal an. Sie sollten in Chrome Task Manager sehen, dass Speicher alloziert und niemals freigegeben wird. 

![Beispiel eines Memory Leaks](/images/memory-leak-example.png)

## Memory-Leak beheben

Im obigen Beispiel können wir unsere `hide()`-Methode verwenden, um einige Aufräumarbeiten durchzuführen und den Memory Leak zu beheben, bevor wir die Select-Komponten aus dem DOM entfernen. Um dies zu bewerkstelligen, werden wir eine Eigenschaft im Datenobjekt unserer Vue-Instanz haben und die Methode `destroy()` der [Choices-API](https://github.com/jshjohnson/Choices) verwenden, um den Speicher freizugeben.

Prüfen Sie jetzt den Speicherverbrauch in diesem [aktualisierten CodePen-Beispiel](https://codepen.io/freeman-g/pen/mxWMor).

```js
new Vue({
  el: "#app",
  data: function () {
    return {
      showChoices: true,
      choicesSelect: null
    }
  },
  mounted: function () {
    this.initializeChoices()
  },
  methods: {
    initializeChoices: function () {
      let list = []
      for (let i = 0; i < 1000; i++) {
        list.push({
          label: "Item " + i,
          value: i
        })
      }
      // Setze eine Referenz auf choicesSelect im Datenobjekt unserer Vue-Instanz
      this.choicesSelect = new Choices("#choices-single-default", {
        searchEnabled: true,
        removeItemButton: true,
        choices: list
      })
    },
    show: function () {
      this.showChoices = true
      this.$nextTick(() => {
        this.initializeChoices()
      })
    },
    hide: function () {
      // Jetzt könnn wir die Referenz nutzen, um den Speicher freizugeben,
      // bevor wir die Elemente aus dem DOM entfernen
      this.choicesSelect.destroy()
      this.showChoices = false
    }
  }
})
```

## Details über den Wert

Speicherverwaltung und Performance-Tests können leicht vernachlässigt werden, wenn man Features schnell auf den Markt bringen will. Es ist jedoch wichtig, möglichst wenig Speicher zu verbrauchen, da dies die allgemeine Benutzererfahrung beeinflusst.

Denken Sie darüber nach, welche Geräte Ihre Benutzer für den Zugriff auf Ihre Web-Anwendung verwenden und wie ihr normaler Ablauf aussieht. Kann es sein, dass sie Laptops oder mobile Geräte mit wenig Speicher verwenden? Nutzen Ihre Benutzer die Navigation innerhalb der Anwendung intensiv? Wenn eines davon der Fall ist, können Ihnen gute Speicherverwaltungspraktiken helfen, den GAU (Absturzt des Browsers des Benutzers) zu vermeiden. Selbst wenn keine der obigen Aussagen zutrifft, können Sie potentiell eine Degradation der Leistung über einen ausgedehnten Nutzungszeitraum Ihrer Anwendung erleben, wenn Sie nicht aufpassen.

## Realistisches Beispiel

Im Beispiel oben verwendeten wir eine `v-if`-Direktive, um ein Memory Leak zu zeigen. Ein häufigerer Fall ist die Verwendung von [vue-router](https://router.vuejs.org/en/) für die Navigation zu Komponenten in einer Single Page Application.  

Genauso wie die `v-if`-Direktive, entfernt `vue-router` Elemente aus dem virtuellen DOM und ersetzt diese mit neuen Elementen, wenn ein Benutzer in Ihrer Anwendung herumnavigiert. Der `beforeDestroy()`-[Lebenszyklushook](/v2/guide/instance.html#Lifecycle-Diagram) ist ein guter Ort um gas gleiche Problem in einer `vue-router`-basierten Anwendung zu lösen.

Wir können unsere Aufräumarbeiten in einen `beforeDestroy()`-Hook wie folgt platzieren:

```js
beforeDestroy: function () {
    this.choicesSelect.destroy()
}
```

## Alternative Entwurfsmuster

Wir haben die Speicherverwaltung beim Entfernen von Elementen besprochen, wie sollen wir aber vorgehen, wenn wir den Zustand und die Elemente im Speicher behalten wollen? In diesem Fall können Sie die eingebaute Komponente [keep-alive](/v2/api/#keep-alive) verwenden.

Wenn Sie eine Komponente mit `keep-alive` umgeben, wird ihr Zustand beibehalten und bleibgt daher im Speicher.

```html
<button @click="show = false">Hide</button>
<keep-alive>
  <!-- my-component wird absichtlich im Speicher bleiben, selbst, wenn sie entfernt wird -->
  <my-component v-if="show"></my-component>
</keep-alive>
```
Diese Technik kann nützlich sein, um die Benutzererfahrung zu verbessern. Stellen Sie sich zum Beispiel vor, dass ein Benutzer beginnt, Kommentare in ein Textfeld einzugeben und dann zu einem anderen Teil der Anwendung wechselt. Wenn der Benutzer zu den Kommentaren zurückgeht, wird er seinen Text weiterschreiben können. 

Wenn Sie `keep-alive` verwenden, haben Sie Zugang zu weiteren zwei Lebenszyklushooks: `activated`und `deactivated`. Wenn Sie den Speicher freigeben oder Daten ändern wollen, wenn eine keep-alive-Komponente entfernt wird, können Sie dies im `deactivated`-Hook machen.

```js
deactivated: function () {
  // Entfernen Sie all jene Daten, die nicht im Speicher bleiben sollen
}
```

## Fazit

Vue macht die Entwicklung wunderbarer, reaktiver JavaScript-Anwendungen sehr einfach, aber Sie müssen auf Memory Leaks aufpasen. Diese werden häufig bei Verwendung zusätzlicher Bibliotheken der Drittparteien vorkommen welche das DOM außerhalb von Vue manipulieren. Stellen Sie sicher, dass Ihre Anwendung auf Memory Leaks getestet wird und setzen Sie entsprechende Maßnahmen, falls nötig.
