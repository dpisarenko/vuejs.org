---
title: Editierbare SVG-Icon-Systeme
type: cookbook
order: 4
---

## Grundlegendes Beispiel

Man kann ein SVG-Icon-System auf mehrere Arten erstellen. Aber eine Methode nutzt die Fähigkeiten von Vue, editierbare Inline-Icons als Komponenten zu erstellen. Einige Vorteile davon sind:

* Man kann sie leicht "on the fly" editieren
* Man kann sie animieren
* Du kannst Standard-Eigenschaften und Defaultwerte verwenden, um eine typische Größe zu erzwingen bzw. zu verändern
* Sie sind inline, d. h. es sind keine HTTP-Anfragen nötig
* Sie können dynamisch zugänglich gemacht werden

Zuerst werden wir ein Verzeichnis für alle Icons erstellen. Wir werden sie nach einem standardisierten Muster benennen, um sie leicht wiederfinden zu können:

> components/icons/IconBox.vue
> components/icons/IconCalendar.vue
> components/icons/IconEnvelope.vue

Hier ist eine Beispielrepository, damit Du anfangen kannst. Dort kannst Du das Ganze sehen: [https://github.com/sdras/vue-sample-svg-icons/](https://github.com/sdras/vue-sample-svg-icons/)

![Dokumentations-Webseite](https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/screendocs.jpg 'Demo der Dokumentation')

Wir werden eine Komponente (`IconBase.vue`) für ein Basis-Icon erstellen, welche ein Slot verwendet.

```html
<template>
  <svg xmlns="http://www.w3.org/2000/svg"
    :width="width"
    :height="height"
    viewBox="0 0 18 18"
    :aria-labelledby="iconName"
    role="presentation"
  >
    <title
      :id="iconName"
      lang="en"
    >{{ iconName }} icon</title>
    <g :fill="iconColor">
      <slot />
    </g>
  </svg>
</template>
```

Du kannst Dieses Basis-Icon "as is" verwenden. Das Einzige, was Du vielleicht verändern musst, ist die `viewBox`-Eigenschaft. Die korrekten Werte dafür hängen von der `viewBox` Deiner Icons ab. Im Basisicon machen wir die Eigenschaften `width`, `height`, `iconColor` und den Namen des Icons sodass es dynamisch aktualisiert wird, wenn sich die Eigenschaften verändern. Der Name wird sowohl für den Inhalt von `<title>`, als auch von `id` für die Zugänglichkeit sein.

Unser Script wird wie folgt aussehen. Wir werden einige Default-Werte haben, sodass unser Icon konsistent gerendert wird, es sei denn wir spezifizieren etwas Anderes:

```js
export default {
  props: {
    iconName: {
      type: String,
      default: 'box'
    },
    width: {
      type: [Number, String],
      default: 18
    },
    height: {
      type: [Number, String],
      default: 18
    },
    iconColor: {
      type: String,
      default: 'currentColor'
    }
  }
}
```

Die Wert `currentColor` wird dazu führen, dass der Icon die Farbe des ihn umgebenden Textes erben wird. Wir könnten auch eine andere Farbe übergeben, wenn wir wollten.

Wir können es so verwenden, dass sich nur der Inhalt von `IconWrite.vue` innerhalb des Icon-Tags befindet:

```html
<icon-base icon-name="write"><icon-write /></icon-base>
```

Wenn wir mehrere Größen des Icons haben wollen, können wir das sehr leicht machen:

```html
<p>
  <!-- Du kannst einen kleineren Wert für Breite (`width`) und Höhe (`height`) als Eigenschaften übergeben -->
  <icon-base
    width="12"
    height="12"
    icon-name="write"
  ><icon-write /></icon-base>
  <!-- Du kannst auch den Default-Wert von 18 verwenden -->
  <icon-base icon-name="write"><icon-write /></icon-base>
  <!-- Oder das Icon ein wenig vergrössern :) -->
  <icon-base
    width="30"
    height="30"
    icon-name="write"
  ><icon-write /></icon-base>
</p>
```

<img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/Screen%20Shot%202018-01-01%20at%204.51.40%20PM.png" width="450" />

## Animierbare Icons

Komponenten für Icons sind hilfreich, wenn Du sie animieren willst, insbesondere in einer Interaktion. Inline SVGs unterstützen die Interatktionen am Besten. Hier ist ein sehr einfaches Beispiel eines Icons, welches nach einem Klick animiert wird:

```html
<template>
  <svg
    @click="startScissors"
    xmlns="http://www.w3.org/2000/svg"
    viewBox="0 0 100 100"
    width="100"
    height="100"
    aria-labelledby="scissors"
    role="presentation"
  >
    <title
      id="scissors"
      lang="en"
    >Scissors Animated Icon</title>
    <path
      id="bk"
      fill="#fff"
      d="M0 0h100v100H0z"/>
    <g ref="leftscissor">
      <path d="M..."/>
      ...
    </g>
    <g ref="rightscissor">
      <path d="M..."/>
      ...
    </g>
  </svg>
</template>
```

```js
import { TweenMax, Sine } from 'gsap'

export default {
  methods: {
    startScissors() {
      this.scissorAnim(this.$refs.rightscissor, 30)
      this.scissorAnim(this.$refs.leftscissor, -30)
    },
    scissorAnim(el, rot) {
      TweenMax.to(el, 0.25, {
        rotation: rot,
        repeat: 3,
        yoyo: true,
        svgOrigin: '50 45',
        ease: Sine.easeInOut
      })
    }
  }
}
```

Wir wenden `refs` auf Gruppen der Pfade an, die wir bewegen müssen. Weil beide Seiten der Schere sich in Tandem bewegen müssen, werden wir eine Funktion erstellen, die wir wiederverwenden und in die wir `refs` übergeben werden. Die Verwendung von GreenSock hilft uns, Probleme bei der Unterstützung der Animiation und der `transform-origin`-bezogenen Fehler in unterschiedlichen Browsern, zu beheben.

<p data-height="300" data-theme-id="0" data-slug-hash="dJRpgY" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Editable SVG Icon System: Animated icon" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/dJRpgY/">Editable SVG Icon System: Animated icon</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p><script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

<p style="margin-top:-30px">Das haben wir ziemlich leicht bewerkstelligt! Eine einfache Aktualisierung im laufenden Betrieb.</p>

Du kannst mehr animierte Beispiele im Repository [hier](https://github.com/sdras/vue-sample-svg-icons/) finden.

## Zusätzliche Anmerkungen

Designers können ihre Meinungen ändern. Anforderungen an das Produkt können sich verändern. Wenn Du die Logik des gesamten Icon-Systems in einer Grundkomponente hältst, kannst Du schnell alle Deine Icons aktualisieren und sicher sein, dass diese Änderungen sich im gesamten System verbreiten werden. Selbst bei Verwendund eines Iconladers kann es vorkommen, dass Du jede SVG-Datei neu erstellen oder bearbeiten musst. Diese Methode kann Dir diese Zeit und Schmerzen sparen.

## Wann Du dieses Muster nicht anwenden sollst

Diese Art eines SVG-Icon-Systems is nützlich, wenn Du mehrere Icons hast, die auf unterschiedliche Arten auf der gesamten Seite verwendet werden. Wenn Du dagegen ein und dasselbe Icon mehrmals auf gleicher Seite verwendest (z. B. das "Entfernen"-Icon in jeder Zeile einer riesigen Tabelle), dann könnte es sinnvoller sein, alle Sprites zu einem Sprite-Sheet zu kompilieren und die `<use>`-Tags zu verwenden, um sie zu laden.

## Alternative Muster

Andere Werkzeuge für die Verwaltung von SVG-Icons sind, unter Anderem:

* [svg-sprite-loader](https://github.com/kisenka/svg-sprite-loader)
* [svgo-loader](https://github.com/rpominov/svgo-loader)

Diese Tools verpacken SVGs zur Compile-Zeit, machen es aber etwas schwerer, sie zur Laufzeit zu editieren, weil `<use>`-Tags eigenartige Cross-Browser-Probleme haben können, wenn Du etwas Komplexeres mit ihnen machst. Wenn Du sie verwendest, hast Du außerdem zwei `viewBox`-Eigenschaften und dadurch zwei Koordinatensysteme. Das macht die Implementierung etwas komplexer.