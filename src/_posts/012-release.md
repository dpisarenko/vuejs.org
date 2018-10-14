---
title: Vue.js 0.12 veröffentlicht!
date: 2015-06-11 17:37:30
---

Es freut mich euch mitteilen zu können, dass [Vue.js 0.12: Dragon Ball](https://github.com/yyx990803/vue/releases/tag/0.12.0) endlich hier ist! Vielen Dank an all diejenigen, die Feedback zur beta/rc Version gegeben haben.

Es hat sich Vieles geändert und wir werden das Wichtigste unten hervorheben. Wir empfehlen trotzdem die [Full Release Note](https://github.com/yyx990803/vue/releases/tag/0.12.0) und die aktualisierte Dokumentation zu lesen, wenn du ein Upgrade von 0.11 machen willst. Du kannst Bugs auf GitHub melden, Fragen in [vuejs/Discussion](https://github.com/vuejs/Discussion/issues) stellen oder unserem [Gitter chat channel](https://gitter.im/yyx990803/vue) beitreten.

<!-- more -->

### Konsistente Komponentensyntax

In 0.11 hast du zwei Möglichkeiten gehabt Komponenten zu benutzen: Mit der Direktive `v-component` oder mit einem benutzerdefinierten Element. Ebenso hattest du zwei Möglichkeiten Daten an Kinden zu übergeben: Mit der Direktive `v-with` oder der Option `paramAttributes`. Sowohl benutzerdefinierte Elemente als auch `paramAttributes` werden zu Direktiven kompiliert und sind damit verwirrend und überflüssig.

Zusätzlich, Komponentensyntax ist ein First-Class Konzept in Vue.js und damit wichtiger als Direktive. Es definiert wie View-Logik gekapselt wird. Ein klarer und deklarativer Weg zum Übergeben von Daten an Kind-Komponenten ist ebenfalls wichtig. Komponenten und Parameter verdienen ihre eigene Syntax damit sie von anderen Direktiven unterschieden werden können.

Deshalb sind `v-component` und `v-with` in 0.12 abgeschrieben und `paramAttributes` zu `props` umbenannt worden. Ab jetzt werden die meissten Komponenten wie folgt in Vue.js aussehen.

``` html
<my-component prop="{{parentData}}"></my-component>
```

Es git weitere Verbesserungen bezüglich Props wie bspw. explizite Einweg-Props, Ausdrücke als Props, Methoden als Props, Callbacks und mehr. Die Details findest du in den 0.12 Release Notes, auf die oben verlinkt wurde oder in der Dokumentation [hier](/guide/components.html).

### Verbesserte Filterargumente

In 0.11 Filterargumente wurden immer als Strings übertragen. Ein Argument in Anführungszeichen kann Whitespace beinhalten, welcher nicht automatisch entfernt wurde wenn das Argument an einen Filter übergeben wurde. Wie man einen dynamischen Wert statt einem String bekommt war auch nicht klar.

In 0.12 gibt es nun eine einfache Regel zu Filtersyntax: Wenn das Argument in Anführungszeichen eingeschlossen ist, dann wird es als String übergeben. Andernfalls wird es als dynamischer Wert behandelt.

Das bedeutet, dass der Umgang mit vorhandenen Filtern sich ändern wird:

``` html
<a v-on="keyup: onKeyUp | key 'enter'"></a>
{{ items.length | pluralize 'item' }}
```

Dafür wird der Umgang mit benutzerdefinierten Filtern viel einfacher:

``` html
{{ msg | concat otherMsg }}
```

Hier ist das erste Argument für `concat` der Wert von `this.otherMsg`.

### Asynchrone Komponenten

Für SPA ist es üblich alles in einer Datei zu bündeln. Wenn aber diese Datei zu groß wird, möchten wir vielleicht einige Teile der Anwendung später laden um das initiale Laden zu beschleunigen. Dies stellt bestimmte Bedingungen an die Architektur der Anwendung. Es ist nicht einfach festzustellen an welcher Stelle deine Bundles aufgeteilt werden können.

Mit Vue.js können wir bereits unsere Anwendung aus losgelöste Komponente bauen. Wäre es nicht klasse, wenn wir eine dynamische Komponente nur dann laden könnten wenn wir sie auch brauchen? In der Tat, mit 0.12 ist genau das möglich um trivial umzusetzen mit Asynchronen Komponenten.

In 0.12 kannst du Komponenten als Factory-Funktion definieren, welche asynchron aufgelöst werden. Vue.js wird sie nur dann anstoßen wenn die Komponente tatsächlich gebraucht wird. Die Komponente wird dann im Cache abgelegt für zukünftige Aufrufe.

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

Es liegt an dir zu entscheiden, wie du Komponenten vom Server laden willst, `$.getScript()` oder require.js. Wir empfehlen es mit Webpack's [Code Splitting feature](http://webpack.github.io/docs/code-splitting.html) zu verbinden:

``` js
Vue.component('async-webpack-example', function (resolve, reject) {
  // In Webpack AMD like syntax indicates a code split point
  require(['./my-async-component'], resolve)
})
```

Das ist alles was du machen musst. Du kannst due Komponente so wie vorher nutzen ohne darüber nachzudenken, dass sie asynchron ist. Webpack wird dein Bundle automatisch mit den richtigen Abhängigkeiten aufteilen und es via Ajax dann laden, wenn es benuzt wird. Du kannst dir ein vollständiges Beispiel [hier](https://github.com/vuejs/vue-webpack-example) ansehen.

### Verbessertes Überganssystem

Das Übergangssystem von Vue.js ist wirklich einfach zu benutzen. Es hatte allerdings den Nachteil, dass man nicht CSS zusammen mit auf JavaScript basierten Übergängen nutzen konnte. In 0.12 ändert sich das! Das verbesserte Übergangssystem erlaubt es dir JavaSScript-Hooks zu CSS basierten Übergängen hinzuzufügen. Die Anzahl der Hooks wurde zusätzlich erweitert, sodass du maximale Kontrolle bei jedem Schritt behälst.

`v-repeat` kommt jetzt mit eingebautem Support für gestaffelte Übergänge. Du brauchst nur `stagger="100"` hinzuzufügen. Es ist auch möglich seperate Staffelungen für das Betreten und Verlassen hinzuzufügen und die Verzögerung der Staffelung dynamisch in einer JavaScript-Hook zu berechnen.

Für weitere Details, kannst du das [aktualisierte Handbuch](/guide/transitions.html) benutzen.

### Performance Tuning

Vue.js' präzises Tracking von Abhängigkeiten macht es zu einem der effizientesten View-Schichten für kleine Updates. Man kann sich aber immer verbessern und mit 0.12 ist das Rendern von langen Listen, mithilfe von internen Instanzen und Verbesserungen des Kompilierens um bis zu 40% schneller. Mit dem korrekten EInsatz von `track-by` ist das [Re-Rendering mit großen, neuen Datasets](http://vuejs.github.io/js-repaint-perfs/vue/) vergleichbar schnell oder schneller als andere Virtual-DOM basierte Frameworks.

### Noch eine Sache...

Da 0.12 jetzt abgeschlossen ist, wird wieder mehr Wert auf die Weiterentwicklung von vue-router gelegt, die dedizierte Routing-Library für Vue.js mit Verschachtelung von Views, Support für Übergänge und asynchronen Data-Hooks. Wie bereits vorher erwähnt wird Vue.js selbst eine einfache View-Schicht bleiben. Der vue-router ist seperat erhältlich und optional. Du kannst allerdings erwarten, dass alles zusammen funktioniert wenn du es brauchst.
