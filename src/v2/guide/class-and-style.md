---
title: Klassen- und Stilbindungen
type: guide
order: 6
---

Eine häufige Anforderung an Datenbindung ist das Bearbeiten der Klassenliste und der Inline-Stile eines Elements. Da sie beide Attribute sind, können wir `v-bind` verwenden, um sie zu behandeln: Wir müssen nur einen finalen String mit unseren Ausdrücken berechnen. Jedoch ist das Hantieren mit Stringverkettung müsham und fehleranfällig. Daher stellt Vue besondere Erweiterungen für jene Fälle zur Verfügung, wo `v-bind` mit `class` und `style` verwendet wird. Zusätzlich zu Strings, können die Ausdrücke auch Objekte und Arrays auswerten.

## HTML-Klassen binden

### Objektsyntax

Wir können ein Objekt an `v-bind:class` übergeben, um dynamisch Klassen ein- und auszuschalten:

``` html
<div v-bind:class="{ active: isActive }"></div>
```

Die obige Syntax bedeutet, dass die Klasse `active` vorhanden sein wird, wenn die Dateneigenschaft `isActive` [wahr](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) ist.

Du kannst mehrere Klassen ein- und ausschalten, indem Du zusätzliche Felder in das Objekt einführst. Außerdem kann die `v-bind:class`-Direktive mit dem einfachen `class`-Attribut koexistieren. Nehmen wir an, wir haben folgende Vorlage:

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

Und folgende Daten:

``` js
data: {
  isActive: true,
  hasError: false
}
```

Dann wird Folgendes gerendert:

``` html
<div class="static active"></div>
```

Wenn sich `isActive` oder `hasError` verändert, wird die Klassenliste entsprechend aktualisiert. Zum Beispiel, wenn `hasError` `true` wird, wird aus der Klassenliste `"static active text-danger"` werden.

Das gebundene Objekt braucht nicht inline sein:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

Das wird zum gleichen Ergebnis gerendert. Wir können auch an eine [berechnete Eigenschaft](computed.html) binden, die ein Objekt zurückgibt. Das ist ein übliches und leistungsfähiges Muster:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Array-Syntax

Wir können ein Array an `v-bind:class` übergeben, um eine Liste von Klassen anzuwenden:

``` html
<div v-bind:class="[activeClass, errorClass]"></div>
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Dies wird folgendes folgendermassen gerendert:

``` html
<div class="active text-danger"></div>
```

Wenn Du in der Lage sein willst, eine Klasse in der Liste ein- und auszuschalten, kannst Du es mit Hilfe eines ternären Ausdrucks erreichen:


``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

Dies wird die Klasse `errorClass` immer anwenden, `activeClass` jedoch nur, wenn `isActive` wahr ist.

Es kann aber etwas langatmig werden, wenn Du mehrere bedingte Klassen hast. Aus diesem Grund ist es möglich, die Objekt-Syntax innerhalb der Array-Syntax zu verwenden:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### Mit Komponenten

> Dieser Abschnitt setzt die Kenntnis der [Vue-Komponenten](components.html) voraus. Du kannst ihn jetzt überspringen und später zurückkommen.

Wenn Du das `class`-Attribut bei einer benutzerdefinierten Komponente verwendest, werden die jeweiligen Klassen zum Wurzelelement der Komponente hinzugefügt. Bestehende Klassen dieses Elements werden nicht überschrieben.

Angenommen, Du deklarierst folgende Komponente:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Dann fügen wir einige Klassen während ihrer Verwendung hinzu:

``` html
<my-component class="baz boo"></my-component>
```

Der gerenderte HTML-Code wird folgendermassen aussehen:

``` html
<p class="foo bar baz boo">Hi</p>
```

Das Gleiche trifft auf Klassenbindungen zu:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

Wenn `isActive` wahr ist, wird der gerenderte HTML-Code so aussehen:

``` html
<p class="foo bar active">Hi</p>
```

## Inline-Stile binden

### Objektsyntax

Die Objektsyntax für `v-bind:style` ist ziemlich einfach - sie sieht fast genauso aus wie CSS, außer dass es ein JavaScript-Objekt ist. Du kannst kannst entweder camelCase oder kebab-kase verwenden (verwende einfache Hochkommas im Fall von kebab-case), um die Namen der CSS-Eigenschaften anzugeben:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

Es ist oft gut, direkt an ein Stil-Objekt zu binden, sodass die Vorlage sauberer wird:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Wiederum wird die Objektsyntax häufig in Verbindung mit berechneten Eigenschaften verwendet, die Objekte zurückgeben.

### Arraysyntax

Die Array-Syntax für `v-bind:style` erlaubt es, mehrere Stil-Objekte auf das gleiche Element anzuwenden:

``` html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### Präfixe automatisch hinzufügen

Wenn Du eine CSS-Eigenschaft verwendest, die [Herstellerpräfixe](https://developer.mozilla.org/de/docs/Glossary/Herstellerpr%C3%A4fix) in `v-bind:style` erfordert, z. B. `transform`, dann wird Vue die entsprechenden Präfixe automatisch entdecken und zu den Namen der angewandten Stile hinzufügen.

### Mehrere Werte

> 2.3.0+

Ab 2.3.0+ kannst Du ein Array von mehreren Werten (mit Präfixen) an die Stil-Eigenschaft übergeben, z. B.:

``` html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Dies wird nur den letzten Wert im Array rendern, den der Browser unterstützt. Dieses Beispiel wird in Browsern, die Version von flexbox ohne Präfixe unterstützen als `display: flex` gerendert.
