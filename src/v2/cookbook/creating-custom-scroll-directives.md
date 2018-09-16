---
title: Erstellen benutzerdefinierter Scrolling-Direktiven
type: cookbook
order: 7
---

## Grundlegendes Beispiel

Oft wollen wir, dass beim Scrollen ein bestimmtes Verhalten, z. B. eine Animation, ausgelöst wird. Man kann das auf mehrere Arten bewerkstelligen, aber der Weg des kleinsten Codes und Abhängigkeiten ist wahrscheinlich die Verwendung einer [benutzerdefinierten Direktive](https://vuejs.org/v2/guide/custom-directive.html). Damit machen wir einen Hook für alles, was nach einem bestimmten Scroll-Ereignis passieren soll.

```js
Vue.directive('scroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      if (binding.value(evt, el)) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})

// Hauptanwendung
new Vue({
  el: '#app',
  methods: {
    handleScroll: function (evt, el) {
      if (window.scrollY > 50) {
        el.setAttribute(
          'style',
          'opacity: 1; transform: translate3d(0, -10px, 0)'
        )
      }
      return window.scrollY > 100
    }
  }
})
```

```html
<div id="app">
  <h1 class="centered">Scrolle mich</h1>
  <div
    v-scroll="handleScroll"
    class="box"
  >
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. A atque amet harum aut ab veritatis earum porro praesentium ut corporis. Quasi provident dolorem officia iure fugiat, eius mollitia sequi quisquam.</p>
  </div>
</div>
```

<p class="tip">Achtung! Die Direktive muss vor der Vue-Instanz registriert werden.</p>

Wir würden auch eine Stil-Eigenschaft brauchen, die die CSS-Werte langsam verändern wird. In diesem Fall sieht sie so aus:

```css
.box {
  transition: 1.5s all cubic-bezier(0.39, 0.575, 0.565, 1);
}
```

<p data-height="450" data-theme-id="5162" data-slug-hash="983220ed949ac670dff96bdcaf9d3338" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Custom Scroll Directive- CSS Transition" class="codepen">See the Pen <a href="https://codepen.io/sdras/pen/983220ed949ac670dff96bdcaf9d3338/">Custom Scroll Directive- CSS Transition</a> by Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Wir könnten den Code noch einfacher machen, indem wir GreenSock(GSAP) oder jede andere JavaScript-Animationsbibliothek verwenden:

```js
Vue.directive('scroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      if (binding.value(evt, el)) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})

// Hauptanwendung
new Vue({
  el: '#app',
  methods: {
    handleScroll: function (evt, el) {
      if (window.scrollY > 50) {
        TweenMax.to(el, 1.5, {
          y: -10,
          opacity: 1,
          ease: Sine.easeOut
        })
      }
      return window.scrollY > 100
    }
  }
})
```

Wir müssten die vorige CSS-Transition aus dieser Implementierung entfernen, weil dies jetzt in JavaScript umgesetzt ist.

## Vorteile der Verwendung benutzerdefinierter Direktiven

Vue-Direktiven können für viele Aufgaben verwendet werden. Die meisten davon decken allgemeine Anwendungsfälle ab und das kann die Produktivität des Entwicklers stark verbessern. Aber wenn Sie einen Fall haben, für den es keine fertigen Direktiven gibt, ist es auch kein Problem, denn Sie können leicht eine benutzerdefinierte Direktive für Ihre spezifischen Aufgaben erstellen.

Hinzufügen und Entfernen von Scroll-Ereignissen von Elementen ist ein richtig guter Anwendungsfall für die Technik. Genauso wie andere von uns verwendete Direktiven, sind sie an ein Element gebunden. Wäre dies nicht der Fall, hätten wir die Referenz dafür im DOM finden müssen. Durch die Verwendung einer Direktive vermeiden wir das Durchgehen des DOM. Außerdem bleibt dadurch die Logik der Ereignisbearbeitund in der Nähe des Knotens, auf den sie sich bezieht.

## Beispiel aus der realen Welt: Verwendung benutzerdefinierter Scrolling-Direktiven für kaskadierende Animationen

Wenn Sie eine zusammenhängende Webseite erstellen, werden Sie feststellen, dass Sie ein und diesselbe Animationslogik in mehreren Bereichen verwenden. Es sieht einfach aus: Wir würden ganz einfach eine sehr spezifische benutzerdefinierte Direktive machen, richtig? Aber wenn Sie sie wiederverwenden, wird es notwendig sein, sie für jeden Fall _ein wenig_ zu verändern.

Um den Code kurz und lesbar zu halten, wollen wir einige vordefinierte Argumente übergeben, z. B. Start- und Endpunk der Animation während des Scrollens.

**Dieses Beispiel sieht im [Vollbildmodus](https://s.codepen.io/sdras/debug/078c19f5b3ed7f7d28584da450296cd0) am Besten aus.**

<p data-height="500" data-theme-id="5162" data-slug-hash="c8c55e3e0bba997350551dd747119100" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Scrolling Example- Using Custom Directives in Vue" class="codepen">See the Pen <a href="https://codepen.io/sdras/pen/c8c55e3e0bba997350551dd747119100/">Scrolling Example- Using Custom Directives in Vue</a> by Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Im Demo oben, hat jeder Abschnitt zwei unterschiedliche Arten der Animation, die durch das Scrollen ausgelöst werden: Morph-Animation und eine Zeichenanimation, die die einzelnen Pfade im SVG animiert. Wir verwenden die Animationen wieder, daher können wir eine benutzerdefinierte Direktive für jeden von ihnen erstellen. Die Argumente, die wir übergeben, werden uns helfen, alles einfach und wieverwendbar zu gestalten.

Um zu zeigen, wie das gemacht wird, werden wir einen Blick auf das Beispiel mit der morphenden Gestalt werfen. Dort müssen wir den Start- und Endpunkt definieren, aber auch den Pfad-Wert für das Morphing übergeben. Jedes dieser Argumente ist als `binding.value.foo` definiert:

```js
Vue.directive('clipscroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      var hasRun = false
      if (!hasRun && window.scrollY > binding.value.start) {
        hasRun = true
        TweenMax.to(el, 2, {
          morphSVG: binding.value.toPath,
          ease: Sine.easeIn
        })
      }
      if (window.scrollY > binding.value.end) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})
```

Wir können dann diese Animation in unserer Vorlage verwendent. In diesem Fall fügen wir die Direktive zum `clipPath`-Element hinzu. Wir übergeben alle unsere Argumente an die Direktive in einem Objekt.

```html
<clipPath id="clip-path">
  <path
    v-clipscroll="{ start: '50', end: '100', toPath: 'M0.39 0.34H15.99V22.44H0.39z' }"
    id="poly-shapemorph"
    d="M12.46 20.76L7.34 22.04 3.67 18.25 5.12 13.18 10.24 11.9 13.91 15.69 12.46 20.76z"
  />
</clipPath>
```

## Alternative Entwurfsmuster

Benutzerdefinierte Direktiven können extrem hilfreich sein. Jedoch kann es vorkommen, dass Sie eine sehr spezifische Funktionalität benötigen, die bereits in einer Scrolling-Bibliothek umgesetzt ist. Dann brauchen Sie diese Lösung nicht von Null neuentwickeln.

[Scrollmagic](http://scrollmagic.io/) hat ein Ökosystem, das reich an Scrolldirektiven ist. Es ist auch gut dokumentiert und hat Demos, mit denen Sie es erkunden können. Darunter finden Sie Sachen wir [parallax](http://scrollmagic.io/examples/advanced/parallax_scrolling.html), [kaskadierendes Anpinnen](http://scrollmagic.io/examples/expert/cascading_pins.html), [Auslöschen der Abschnitte](http://scrollmagic.io/examples/basic/section_wipes_natural.html) und [reagierende Dauer](http://scrollmagic.io/examples/basic/responsive_duration.html).
