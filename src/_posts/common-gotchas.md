---
title: Häufige Beginner-Gotchas
date: 2016-02-06 10:00:00
---

Es gibt einige häufig gestellte Fragen von neuen Vue.js Nutzern. Obwohl alles im Handbuch erklährt ist, kann es in Notfall schwer sein die nötigen Informationen zu finden. Desshalb haben wir diese Liste zusammengestellt, die euch hoffentlich etwas Zeit spart!

<!-- more -->

### Warum wird das DOM nicht aktualisiert?

Meistens, wenn du Daten auf der Instanz von Vue änderst, wird das Vue aktualisiert. Es git jedoch zwei Ausnahmen:

1. Wenn du eine **neue Egenschaft hinzufügst** nachdem die Daten bereits verarbeitet wurden. Aufgrund ES5 Limitierungen und damit das Verhalten konsistent bleibt in allen Browsern, kann Vue.js nicht das Hinzufügen/Löschen von Eingeschaften erkennen. Die best practice ist es diese Eigenscahften im Vorfeld zu deklarieren. Wenn du unbedingt zur Laufzeit neue Eigenschaften setzen musst, kannst du die Methoden [`Vue.set`](/api/#Vue-set) und [`Vue.delete`](/api/#Vue-delete) verwenden.

2. Wenn du ein Index in einem Array setzt (z.B `arr[0] = val`) oder die Eigenschaft `length` bearbeitest. Vue.js kann diese Änderungen nicht erkennen. Bearbeite Arrays mit Methoden der Array-Instanz oder indem du es komplett ersetzt. Vue stellt zusätzlich die Methode `arr.$set(index, value)` zur Verfügung, was syntaktischer Zucker ist für `arr.splice(index, 1, value)`.

Weiteres: [Reaktivität im Detail](/guide/reactivity.html) und [Array-Änderung Erkennung](http://vuejs.org/guide/list.html#Array-Change-Detection).

### Wann wird das DOM aktualisiert?

Vue.js benuzt eine asynchrone Queue um DOM-Updates stapelweise zu verarbeiten. Wenn du Daten änderst werden diese nicht sofort im DOM reflektiert, sondern werden asynchron angewandt. Wie kannst du also wissenw wann das DOM aktualisert wird? Du kannst `Vue.nextTick` unmittelbar nachdem du Daten änderst vevrwenden. Die Callback-Funktion wird ausgeführt wenn nachdem das DOM aktualisert worden ist.

Weiteres: [Async Update Queue](/guide/reactivity.html#Async-Update-Queue).

### Warum ist `data` eine Funktion?

In den einfachen Beispielen wird `data` direkt als ein Objekt deklariert. Das funktioniert, weil wir nur eine Instanz mit `new Vue()` erzeugen. Wenn du aber eine **Komponente** definierst, dann muss `data` eine Funktion sein, welche das initiale data-Objekt zurück gibt. Warum? Weil mehrere Instanzen mit der gleichen Definition erzeugt werden. Wenn wir ein einfaches Objekt für `data` verwenden, dann wird dieses **von allen Instanzen benuzt**! Mit der Funktion `data` können wir eine neue Kopie der initialen Daten für jede Instanz bekommen.

Weiteres: [Component Option Caveats](/guide/components.html#Component-Option-Caveats).

### HTML Groß/Keinschreibung

Alle Templates in Vue.js ist valides, parsbares HTML und Vue.js ist auf konforme Parser angewiesen um diese zu verarbeiten. Nach der Spezifikation wird Groß/Kleinschreibung in HTML für Tags und Attribute ignoriert, d.h. Attribute in camelCase `:myProp="123"` stimmen mit `:myprop="123"` überein. Als Daumenregel solltest du camelCase in JavaScript und kebab-case in Templates verwenden, d.h. die Prop `myProp` in JavaScript sollte in Templates als `:my-prop` verknüpft werden.

Weiteres: [camelCase vs. kebab-case](http://vuejs.org/guide/components.html#camelCase-vs-kebab-case).

Wir ziehen derzeit die Möglichkeit in Betracht diese Unregelmäßigkeit aufzulösen, indem wie Props und Komponenten so verarbeiten, dass Groß/Keinschreibung beachtet wird. Nimm Teil an der Diskussion [hier](https://github.com/vuejs/vue/issues/2308).
