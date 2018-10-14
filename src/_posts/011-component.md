---
title: 0.11 Komponenten-Tips
date: 2014-12-08 15:02:14
tags:
---

<p class="tip">Notiz: Dieser Post enthält Informationen für die veraltete 0.11 Version. Die Änderungen der API sind in den [0.12 release notes](https://github.com/yyx990803/vue/releases) aufgeführt.</p>

Die Veröffentlichung von 0.11 hat [viele Änderungen](https://github.com/yyx990803/vue/blob/master/changes.md) eingeführt und die wichtigste ist wie der Scope von Komponenten funktioniert. In 0.10.x, Komponenten haben ihren Scope geerbt, d.h. in der Kind-Komponente konnte man auf die Eigenschaften im Scope der Eltern zugreifen. Das führt häufig zu eng gekoppelten Komponenten, in denen die Kind-Komponente annimmt zu wissen welche Eigenschaften die Eltern haben. Zusätzlich war es möglich ausversehen den Scope der Eltern zu referenzieren.

<!-- more -->

### Isolierter Scope und die Übergabe von Daten

Mit 0.11 alle Kind-Komponenten haben ihren eigenen isolierten Scope. Es wird emfohlen auf die Daten via [Explicit Data Passing](/guide/components.html#Explicit_Data_Passing) zuzugreifen [`v-with`](/api/directives.html#v-with) oder [`paramAttributes`](/api/options.html#paramAttributes).

`paramAttributes` erlaubt uns Templates im Style von Web Components zu schreiben:

``` js
Vue.component('my-component', {
  paramAttributes: ['params'],
  compiled: function () {
    console.log(this.params) // passed from parent
  }
})
```

``` html
<my-component params="{{params}}"></my-component>
```

### Wo gehört das hin?

In 0.10 alle Direktive einer Komponente wurden im Scope der Kind-Komponenten kompiliert. Das hat funktioniert weil der Scope geerbt wurde. Mit 0.11.1 wollen wir den Scope der Komponenten sauberer aufteilen. Die Dauemenregel ist: Wenn etwas im Eltern-Termplate auftaucht wird es im Eltern-Scope kompoiliert; Wenn es im Kind-Template auftaucht, wird es im Kind-Scope kompiliert. Zum Beispiel:

``` html
<!-- parent template -->
<div v-component="child" v-on="click:onParentClick">
  <p>{{parentMessage}}</p>
</div>
```

``` html
<!-- child template, with replace: true -->
<div v-on="click:onChildClick">
  <h1>{{childMessage}}</h1>
  <content></content>
</div>
```

Alles im Eltern-Template wird im Eltern-Scope kompiliert, inklusive dem Inhalt der an die Kind-Komponente übergeben wird.

Die einzige Ausnahme ist `v-with` (und `paramAttributes`, dass zu `v-with` kompiliert wird), dass in beiden Fällen funktioniert - Du braucht dir also nicht allzu viele Gedanken darüber machen.

### Saubere Event-Kommunikation

In vorhergehenden Versionen war es üblich für Kind-Komponenten via Events mit Eltern zu kommunizieren. Mit dieser Methode ist allerdings nicht garantiert das die Listener auf der Eltern-Komponente ausschließlich auf die gewünschte Kind-Komponente hören. Es war auch möglich Listener höher in der Hierarchie auszulösen.

Der am meist gebräuchliste Fall ist , dass eine Eltern-Komponente auf eine bestimmte Kind-Komponente reagieren muss. Desshalb wurde in 0.11.4, [eine neue Direktive `v-events`](/api/directives.html#v-events) eingeführt.

0.11.4 wurde bereits veröffentlicht, probiers aus!
