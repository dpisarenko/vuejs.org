---
title: Vue.js 0.10 ist hier!
date: 2014-03-22 19:00:13
type: '{{type}}'
yield: '{{>yield}}'
---

Vue.js 0.10.0 (Blade Runner) wurde veröffentlicht! Diese Version kommt mit vielen zusätzlichen Erweiterungen basierend auf den Vorschlägen von Usern, insbesondere Interpolation in literalen Direktiven, dynamischen Komponenten mit der neuen `v-view` Directive, Array-Filtern und der konfigurierbaren Delimeter für Interpolation. Intern wurde die Codebase überarbeitet und verbessert sodass Vue.js nun [noch schneller](http://vuejs.org/perf/) ist.

<!-- more -->

Siehe die [Installation](/guide/installation.html) Seite für die aktuellen Builds.

### Neu

- Literale Directive können nun Interpolationstags beinhalten. Diese Tags werden nur einmal während des Kompilierens ausgewertet. So kann man mit z.B. `v-component="{{type}}"`bedingungsweise entscheiden welche Komponenten instanziiert werden sollen. [Doku](/guide/directives.html#Literal_Directives).
- Attribute, welche in `paramAttributes` gelistet sind akzeptieren nun auch Mustache-Interpolation und werden ebenfalls nur einmal ausgewertet.
- `v-repeat` akzeptiert nun ein Argument, dass als Identifier für das umschlossene Objekt benuzt wird. Dies erlaubt einen expliziten Zugriff auf Eigenschaften in Repeatern. [Doku](/guide/list.html#Using_an_Identifier).
- Es wurde die `v-view` Direktive hinzugefügt, welche mit einem String verbunden wird und dynamisch verschiedene Komponenten instanziieren kann. Der String wird dabei als Komponenten-ID  benuzt. [Doku](/api/directives.html#v-view).
- Es wurden die `filterBy` und `orderBy` Filter für `v-repeat` hizugefügt. [Doku](/api/filters.html#filterBy).
- Benutzerdefinierte Filter, welche auf Eigenschaften im `this` Kontext zugreifen können werden als **Berechnete Filter** bezeichnet. [Doku](/guide/custom-filter.html#Filter_Context).
- Du kannst nun auf das Event im `v-on` Handler-Ausdruck mit `$event` zugreifen. Beispiel: `<a v-on="click:handle('hello', $event)">Hello</a>`
- Interpolationsdelimeter können nun via der globalen Konfigurationsption `delimeters` angepasst werden. Beispiel: `Vue.config({ delimiters: ["[", "]"] })` wird die übereinstimmenden Interpolationstags zu `[[ ]]` ändern für String-Verknüpfungen und `[[[ ]]]` für HTML-Verknüpfungen.

### Änderungen

- `{{yield}}` syntax has been deprecated. A Web Components spec compatible content insertion mechanism using `<content>` elements has been introduced. [Doc](/guide/components.html#Content_Insertion).
- To use a component as a custom element, the component ID must now contain a hyphen (`-`). This is consistent with the current custom element spec draft.
- `v-repeat` Arrays' augmented methods have been renamed from `set` to `$set(index, value)` and `remove` to `$remove(index | value)`. The prefix better differentiates them from native methods. The `replace` method has been removed.
- When iterating over an Object with `v-repeat`, the object no longer gets a `$repeater` array. Instead, the object is now augmented with two methods: `$add(key, value)` and `$delete(key)`, which will trigger corresponding view updates.
- `v-if` now creates and destroys a child ViewModel instance when the binding value changes, instead of simply removing/inserting the DOM node. In addition, it can no longer be used with `v-repeat`. Use `v-show` or the new built-in array filters instead.
- `v-with` can no longer be used alone. It now must be used with either `v-component` or `v-view`. `v-component` can also be used as an empty directive just to create a child VM using the default `Vue` constructor.
- Production build now strips all warnings and debug logs. To leverage `debug: true`, use the development version. The development version now has more detailed warning messages.

### Korrekturen

- `event.stopPropagation()` und `event.preventDefault()` in `v-on` Handlern funktionieren nun wir erwartet.
- `parent` Option funtioniert nun auch in `Vue.extend`
- Mustache-Verknüpfungen in `<textarea>` werden nun korrekt interpoliert bevor diese als Wert gesetzt werden.

### Interna

- `v-component`, `v-with` und `v-if` wurden neu geschrieben für einen saubereren Kompilierungsfluss.
- `v-repeat` wurde neu geschrieben und benutzt jetzt einen überarbeiteten Diff Algorithmus, welcher minimale DOM-Änderungen auslöst wenn das Array auf einer anderen Instanz gesetzt ist, welche überlappende Elemente beinhaltet. Damit kann ein Array effizient gefiltert werden.
- `template` klont jetzt direkt den Inhalt des nativen `<template>` wenn verfügbar.
- Allgemeine Performance-Verbesserungen für Initialisierung und Rendering.
