---
title: Vergleich mit anderen Frameworks
type: guide
order: 801
---

Diese Seite war am Schwierigsten zu schreiben in der gesamten Anleitung. Jedoch glauben wir, dass sie wichtig ist. Wahrscheinlich hast Du bereits Aufgaben gehabt, die Du versucht hast, zu lösen und Du hast dafür eine andere Bibliothek verwendet. Du bist hier, weil Du wissen willst, ob Vue Deine spezifischen Probleme besser lösen kann. Diese Frage hoffen wir hier zu beantworten.

Wir bemühen uns, möglichst unvoreingenommen zu sein. Als Kernteam mögen wir Vue sehr. Es gibt bestimmte Aufgaben, die es besser löst als alles Andere auf dem Markt. Hätten wir das nicht geglaubt, dann würden wir nicht daran arbeiten. Wir wollen jedoch fair und genau sein. Dort wo andere Bibliotheken signifikante Vorteile bieten, versuchen wir, diese auch aufzulisten. Dies gilt z. B. für das umfangreiche Ökosystem von alternativen Renderern bei React oder die unterstützung von Browsern bis IE6 bei Knockout.

Wir wollen auch, dass **Du** uns hilfst, dieses Dokument aktuell zu halten, weil sich die JavaScript-Welt rasch bewegt! Wenn Du eine Ungenauigkeit oder etwas Falsches bemerkst, lass uns bitte wissen, indem Du [ein Issue öffnest](https://github.com/vuejs/vuejs.org/issues/new?title=Inaccuracy+in+comparisons+guide).

## React

React und Vue haben viel gemeinsam. Beide

- verwenden ein virtuelles DOM
- stellen reaktive und zusammensetzbare Ansichtskomponenten zur Verfügung
- konzentrieren sich auf die Kernbibliothek und behandeln Sachen wie Routing und globale Zustandsverwaltung in Begleitbibliotheken

Weil sie so ähnlich sind, haben wir am mehr Zeit in Feinabstimmung des Vergleichs mit React investiert, als in Vergleiche mit allen anderen Bibliotheken. Wir wollen nicht nur technische Genauigkeit, sondern auch das Gleichgewicht sicherstellen. Wir zeigen, wo React Vue überstrahlt, z. B. in der Reichhaltigkeit deren Ökosystems und im Überfluss ihrer benutzerdefinierten Renderer.

Dennoch ist es unvermeidlich, dass dieser Vergleich einigen Benutzern voreingenommen zugunsten Vue erscheinen wird, weil viele der besprochenen Themen zu einem gewissen Grad subjektiv sind. Wir erkennen die Existend unterschiedlicher technischer Geschmäcker an. Dieser Vergleich zielt primär darauf ab, die Gründe zu umreissen, warum Vue potentiell eine bessere Wahl sein kann, wenn Deine Präferenzen mit unseren übereinstimmen.

Außerdem könnten einige Teile aufgrund rezenter Veränderungen in React 16+ unten ein wenig veraltet sein. Wir planen, in naher Zukunft mit der React-Community zusammenzuarbeiten, um diesen Abschnitt zu modernisieren.

### Performance zur Laufzeit

Sowohl React, als auch Vue sind außerordentlich und ähnlich schnell. Daher ist es unwahrscheinlich, dass die Geschwindigkeit über die Wahl zwischen ihnen entscheiden wird. Für spezifische Messungen verweisen wir auf dieses [von einer dritten Partei erstelltes Benchmark](http://www.stefankrause.net/js-frameworks-benchmark7/table.html), das sich auf die rohe Render- und Update-Performance mit sehr einfachen Komponentenbäumen konzentriert.

#### Optimierungsbemühungen

In React löst eine Zustandsänderung einer Komponente das Neurendern des gesamten Komponenten-Unterbaums, angefangen bei dieser Komponente als Wurzel. Um unnötiges Rendern der Kindkomponenten zu vermeiden, musst Du entweder `PureComponent` verwenden oder `shouldComponentUpdate` umsetzen, wann immer Du kannst. Du könntest auch unveränderliche Datenstrukturen verwenden, um Deine Zustandsänderungen optimierungsfreundlicher zu machen. Jedoch, kannst Du Dich in bestimmten Fällen nicht auf solche Optimierungen verlassen, weil  `PureComponent/shouldComponentUpdate` annimmt, dass die Render-Ausgabe des gesamten Unterbaums durch die Eigenschaften der aktuellen Komponente bestimmt wird. Ist das nicht der Fall, können solche Optimiereungen zum inkonsistenten DOM-Zustand führen.

In Vue werden die Abhängigkeiten einer Komponente beim Rendern automatisch verfolgt. Dadurch weiss das System exakt, welche Komponenten wirklich neugerendert werden müssen, wenn sich der Zustand verändert. Du kannst es Dir so vorstellen: Bei jeder Komponente ist `shouldComponentUpdate` automatisch implementiert, ohne die Vorbehalte bezüglich verschachtelter Komponenten.

Deswegen ist eine ganze Klasse von Performance-Optimierungen unnötig. Die Entwickler können sich mehr auf das Bauen der eigentlichen, wachsenden Anwendung konzentrieren.

### HTML & CSS

In React ist alles JavaScript. Nicht nur werden HTML-Strukturen als JSX ausgedrückt, der neue Trend geht dazu, auch CSS innerhalb von JavaScript zu verwalten. Dieser Ansatz hat seine Vorteile, ist aber mit Kompromissen verbunden, die nicht für alle Entwickler gut sind.

Vue verwendet klassische Web-Technologien und baut auf ihnen auf. Um zu zeigen, was das bedeutet, werden wir in einige Beispiele eintauchen.

#### JSX gegen Vorlagen

In React drücken alle Komponenten ihre Benutzeroberfläche in Render-Fuktionen mit JSX aus, einer deklarativen XML-ähnlichen Syntax, die innerhalb von JavaScript funktioniert.

Render-Funktionen mit JSX haben mehrere Vorteile:

- Du kannst die Kraft einer richtigen Programmiersprache (JavaScript) zu Deinem grössten Vorteil nutzen, wenn Du Ansichten definierst. Das beinhaltet temporäre Variablen, Flusskontrolle, und das direkte Referenzieren der JavaScript-Werte.

- Die Unterstützung durch Werkzeugen (z. B. Linting, Typüberprüfung, Autovervollständigen im Editor) für JSX ist in bestimmten Bereichen fortgeschrittener als das, was derzeit für Vue-Vorlagen verfügbar ist.

In Vue haben wir ebenfalls [Render-Funktionen](render-function.html) sogar die [JSX-Unterstützung](render-function.html#JSX), weil wir manchmal diese Leistungsfähigkeit brauchen. Meistens verwenden wir jedoch Vorlagen als eine einfachere Alternative. Jedes gültige HTML-Code ist eine gültige HTML-Vorlage, und dies führt zu mehreren Vorteilen:

- Für viele Entwickler, die mit HTML gearbeitet haben, fühlen sich Vorlagen natürlicher zum Lesen und Schreiben an. Die Vorliebe selber ist zwar subjektiv, aber wenn der Entwickler dadurch produktiver wird, ist der Vorteil objektiv.

- HTML-basierte Vorlagen machen es einfacher, existierende Anwendungen schrittweise zu migrieren, um die Vorteile der Reaktivität von Vue zu nutzen.

- Für Designer und weniger erfahrene Entwickler wird das Lesen und Beitragen zum Code viel leichter.

- Du kannst sogar Präprozessoren wie Pug (vormals Jade) verwenden, um Deine Vue-Vorlagen zu erstellen.

Manche behaupten, dass Du eine zusätzliche DSL (Domain-Specific Language) lernen musst, um Vorlagen zu schreiben - wir glauben, dass dieser Unterschied höchstens oberflächlich ist. Erstens, JSX bedeutet nicht, dass der Benutzer nichts zu lernen braucht - es ist eine zusätzliche Syntax, die JavaScript erweitert. Daher kann sie für jemanden, der/die JavaScript schon kann, leicht zu lernen sein. Wenn jemand sagt, dass das Lernen von JSX keinen Lernaufwand verursacht, ist es irreführend. Ähnlich dazu ist eine Vorlage - es ist auch nur eine zusätzliche Syntax für einfaches HTML und hat auch einen sehr geringen Lernaufwand für jene, die mit HTML bereits vertraut sind. Mittels einer DSL können wir dem Benutzer auch helfen, mehr mit weniger Code zu erreichen (z. B. durch `v-on`-Modifikatoren). Die gleiche Aufgabe kann zu mehr Code führen, wenn man einfaches JSX oder Render-Funktionen verwendet.

Auf einer höheren Ebene können wir die Komponenten in zwei Kategorien unterteilen: Präsentation und Logik. Wir empfehlen die Verwendung von Vorlagen für Präsentationskomponenten und Render-Funktionen/JSX für die Logik-Komponenten. Das Verhältnis dieser Komponententypen hängt davon ab, welche Anwendung Du baust, aber im Allgemeinen scheinen die Präsenationskomponenten häufiger zu sein.

#### CSS-Gültigkeitsbereich in Komponenten

In React wird der Gültigkeitsbereich von CSS über CSS-in-JS-Lösungen gemacht (z. B. [styled-components](https://github.com/styled-components/styled-components), [glamorous](https://github.com/paypal/glamorous), und [emotion](https://github.com/emotion-js/emotion)), es sei denn Du verteilst die Komponenten auf mehrere Dateien (z. B. mit [CSS Modules](https://github.com/gajus/react-css-modules)). Das führt ein neues, komponenten-orientiertes Stil-Paradigma, welches sich vom normalen CSS-Entwicklungsprozess unterscheidet. Ausserdem, obwohl das Extrahieren von CSS in eine einzige Datei zur Build-Zeit unterstützt wird, ist es immer noch üblich, eine Runtime in das Paket zu inkludieren, damit die Stile richtig funktionieren. Du kannst zwar von der Dynamik von JavaScript profitieren, wenn Du Deine Stile aufbaust. Der Preis dafür ist jedoch eine höhere Paketgröße und Laufzeitkosten.

Wenn Du ein Fan von CSS-in-JS bist: Viele der beliebten CSS-in-JS-Bibliotheken unterstützen Vue (z. B. [styled-components-vue](https://github.com/styled-components/vue-styled-components) und [vue-emotion](https://github.com/egoist/vue-emotion)). Der Hauptunterschied zwischen React und Vue ist das die Default-Methode für das Stilisieren in Vue die Verwendung von vertrauten `style`-Tags in [1-Datei-Komponenten](single-file-components.html) ist.

[1-Datei-Komponenten](single-file-components.html) geben Dir vollen Zugriff auf CSS in der gleichen Datei wie im Rest des Komponentencodes.

``` html
<style scoped>
  @media (min-width: 250px) {
    .list-container:hover {
      background: orange;
    }
  }
</style>
```

Das optionale `scoped`-Attribut begrenzt den Gültigkeitsbereich dieses CSS-Codes automatisch auf Deine Komponente. Dies geschieht durch das Hinzufügen eines einzigartigen Attributs (wie `data-v-21e5b78`) zu den Elementen. Danach wird `.list-container:hover` zu etwas wie `.list-container[data-v-21e5b78]:hover` kompiliert.

Schliesslich ist das Stilisieren in 1-Dateien-Komponenten sehr flexibel. Mittels [vue-loader](https://github.com/vuejs/vue-loader) kannst Du jeden Präprozessor, Post-Prozessor verwenden und sogar [CSS-Module](https://vue-loader.vuejs.org/en/features/css-modules.html) tief integrieren -- alles innerhalb des `<style>`-Elements.

### Skalierung

#### Nach oben skalieren

Sowohl Vue, als auch React bieten robuste Routing-Lösungen für grosse Anwendungen an. Die React-Community ist auch sehr innovativ in puncto Lösungen für die Zustandsverwaltung (z. B. Flux/Redux). Diese Lösungen und [selbst Redux selbst](https://yarnpkg.com/en/packages?q=redux%20vue&p=1) können leicht in Vue-Anwendungen integriert werden. Vue hat diesen Ansatz mit [Vuex](https://github.com/vuejs/vuex), einer von Elm inspirierten Zustandverwaltungslösung, die stark mit Vue integriert ist, weiterentwickelt. Wir glauben, dass diese Lösung eine bessere Entwicklungserfahrung liefert.

Ein weiterer wesentlicher Unterschied ist dass alle Vues Zustandsverwaltungs- und Routing-Bibliotheken (und auch [andere Anliegen](https://github.com/vuejs)) offiziell unterstützt und mit der Kernbibliothek kompatibel gehalten werden. React dagegen überlässt diese Sorgen der Community, was zu einem fragmentieren Ökosystem führt. Dank grösserer Beliebtheit ist React's Ökosystem reichhaltiger als das von Vue.

Schliesslich bietet Vue ein [Kommandozeilen-Projektgenerator](https://github.com/vuejs/vue-cli) an, der es trivial macht, ein Projekt mit dem Build-System Deiner Wahl zu starten: Du kannst zwischen [Webpack](https://github.com/vuejs-templates/webpack), [Browserify](https://github.com/vuejs-templates/browserify) und [keinem Build-System](https://github.com/vuejs-templates/simple) wählen. React macht ebenfalls Bemühungen in diese Richtung (siehe [create-react-app](https://github.com/facebookincubator/create-react-app)), aber momentan gibt es einige Beschränkungen:

- React erlaubt keine Konfigurierung während der Projektgenerierung, während Vues Vorlagen eine Anpassung im [Yeoman](http://yeoman.io/)-Stil ermöglichen.
- Es enthält nur eine einzige Vorlage und nimmt an, dass Du eine Single-Page-Application baust. Dagegen bietet Vue eine Auswahl an Vorlage für unterschiedliche Zwecke und Build-Systeme an.
- Es kann keine Projekte auf Basis von benutzerdefinierten Vorlagen generieren, was besonders für größere Firmen mit etablierten Konventionen wichtig ist.

Es ist wichtig zu betonen, dass viele dieser Begrenzungen absichtliche Entwurfsentscheidungen des create-react-app-Teams sind und ihre Vorteile haben. Zum Beispiel, solange die Bedürfnisse Deines Projekts sehr simpel sind, und niemals Deinen Build-Prozess anpassen musst, wirst Du es wie eine Abhängigkeit aktualisieren können. Du kannst [hier](https://github.com/facebookincubator/create-react-app#philosophy) mehr über die unterschiedlichen Philosophien lesen.

#### Nach unten skalieren

React is renowned for its steep learning curve. Before you can really get started, you need to know about JSX and probably ES2015+, since many examples use React's class syntax. You also have to learn about build systems, because although you could technically use Babel Standalone to live-compile your code in the browser, it's absolutely not suitable for production.

While Vue scales up just as well as React, it also scales down just as well as jQuery. That's right - to get started, all you have to do is drop a single script tag into the page:

``` html
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

Then you can start writing Vue code and even ship the minified version to production without feeling guilty or having to worry about performance problems.

Since you don't need to know about JSX, ES2015, or build systems to get started with Vue, it also typically takes developers less than a day reading [the guide](./) to learn enough to build non-trivial applications.

### Native Rendering

React Native enables you to write native-rendered apps for iOS and Android using the same React component model. This is great in that as a developer, you can apply your knowledge of a framework across multiple platforms. On this front, Vue has an official collaboration with [Weex](https://weex.apache.org/), a cross-platform UI framework created by Alibaba Group and being incubated by the Apache Software Foundation (ASF). Weex allows you to use the same Vue component syntax to author components that can not only be rendered in the browser, but also natively on iOS and Android!

At this moment, Weex is still in active development and is not as mature and battle-tested as React Native, but its development is driven by the production needs of the largest e-commerce business in the world, and the Vue team will also actively collaborate with the Weex team to ensure a smooth experience for Vue developers.

Another option Vue developers will soon have is [NativeScript](https://www.nativescript.org/), via a [community-driven plugin](https://github.com/rigor789/nativescript-vue).

### With MobX

MobX has become quite popular in the React community and it actually uses a nearly identical reactivity system to Vue. To a limited extent, the React + MobX workflow can be thought of as a more verbose Vue, so if you're using that combination and are enjoying it, jumping into Vue is probably the next logical step.

### Preact and Other React-Like Libraries

React-like libraries usually try to share as much of their API and ecosystem with React as is feasible. For that reason, the vast majority of comparisons above will also apply to them. The main difference will typically be a reduced ecosystem, often significantly, compared to React. Since these libraries cannot be 100% compatible with everything in the React ecosystem, some tooling and companion libraries may not be usable. Or, even if they appear to work, they could break at any time unless your specific React-like library is officially supported on par with React.

## AngularJS (Angular 1)

Some of Vue's syntax will look very similar to AngularJS (e.g. `v-if` vs `ng-if`). This is because there were a lot of things that AngularJS got right and these were an inspiration for Vue very early in its development. There are also many pains that come with AngularJS however, where Vue has attempted to offer a significant improvement.

### Complexity

Vue is much simpler than AngularJS, both in terms of API and design. Learning enough to build non-trivial applications typically takes less than a day, which is not true for AngularJS.

### Flexibility and Modularity

AngularJS has strong opinions about how your applications should be structured, while Vue is a more flexible, modular solution. While this makes Vue more adaptable to a wide variety of projects, we also recognize that sometimes it's useful to have some decisions made for you, so that you can just start coding.

That's why we offer a [webpack template](https://github.com/vuejs-templates/webpack) that can set you up within minutes, while also granting you access to advanced features such as hot module reloading, linting, CSS extraction, and much more.

### Data binding

AngularJS uses two-way binding between scopes, while Vue enforces a one-way data flow between components. This makes the flow of data easier to reason about in non-trivial applications.

### Directives vs Components

Vue has a clearer separation between directives and components. Directives are meant to encapsulate DOM manipulations only, while components are self-contained units that have their own view and data logic. In AngularJS, directives do everything and components are just a specific kind of directive.

### Runtime Performance

Vue has better performance and is much, much easier to optimize because it doesn't use dirty checking. AngularJS becomes slow when there are a lot of watchers, because every time anything in the scope changes, all these watchers need to be re-evaluated again. Also, the digest cycle may have to run multiple times to "stabilize" if some watcher triggers another update. AngularJS users often have to resort to esoteric techniques to get around the digest cycle, and in some situations, there's no way to optimize a scope with many watchers.

Vue doesn't suffer from this at all because it uses a transparent dependency-tracking observation system with async queueing - all changes trigger independently unless they have explicit dependency relationships.

Interestingly, there are quite a few similarities in how Angular and Vue are addressing these AngularJS issues.

## Angular (Formerly known as Angular 2)

We have a separate section for the new Angular because it really is a completely different framework from AngularJS. For example, it features a first-class component system, many implementation details have been completely rewritten, and the API has also changed quite drastically.

### TypeScript

Angular essentially requires using TypeScript, given that almost all its documentation and learning resources are TypeScript-based. TypeScript has its benefits - static type checking can be very useful for large-scale applications, and can be a big productivity boost for developers with backgrounds in Java and C#.

However, not everyone wants to use TypeScript. In many smaller-scale use cases, introducing a type system may result in more overhead than productivity gain. In those cases you'd be better off going with Vue instead, since using Angular without TypeScript can be challenging.

Finally, although not as deeply integrated with TypeScript as Angular is, Vue also offers [official typings](https://github.com/vuejs/vue/tree/dev/types) and [official decorator](https://github.com/vuejs/vue-class-component) for those who wish to use TypeScript with Vue. We are also actively collaborating with the TypeScript and VSCode teams at Microsoft to improve the TS/IDE experience for Vue + TS users.

### Runtime Performance

Both frameworks are exceptionally fast, with very similar metrics on benchmarks. You can [browse specific metrics](http://www.stefankrause.net/js-frameworks-benchmark7/table.html) for a more granular comparison, but speed is unlikely to be a deciding factor.

### Size

Recent versions of Angular, with [AOT compilation](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) and [tree-shaking](https://en.wikipedia.org/wiki/Tree_shaking), have been able to get its size down considerably. However, a full-featured Vue 2 project with Vuex + Vue Router included (~30KB gzipped) is still significantly lighter than an out-of-the-box, AOT-compiled application generated by `angular-cli` (~65KB gzipped).

### Flexibility

Vue is much less opinionated than Angular, offering official support for a variety of build systems, with no restrictions on how you structure your application. Many developers enjoy this freedom, while some prefer having only one Right Way to build any application.

### Learning Curve

To get started with Vue, all you need is familiarity with HTML and ES5 JavaScript (i.e. plain JavaScript). With these basic skills, you can start building non-trivial applications within less than a day of reading [the guide](./).

Angular's learning curve is much steeper. The API surface of the framework is huge and as a user you will need to familiarize yourself with a lot more concepts before getting productive. The complexity of Angular is largely due to its design goal of targeting only large, complex applications - but that does make the framework a lot more difficult for less-experienced developers to pick up.

## Ember

Ember is a full-featured framework that is designed to be highly opinionated. It provides a lot of established conventions and once you are familiar enough with them, it can make you very productive. However, it also means the learning curve is high and flexibility suffers. It's a trade-off when you try to pick between an opinionated framework and a library with a loosely coupled set of tools that work together. The latter gives you more freedom but also requires you to make more architectural decisions.

That said, it would probably make a better comparison between Vue core and Ember's [templating](https://guides.emberjs.com/v2.10.0/templates/handlebars-basics/) and [object model](https://guides.emberjs.com/v2.10.0/object-model/) layers:

- Vue provides unobtrusive reactivity on plain JavaScript objects and fully automatic computed properties. In Ember, you need to wrap everything in Ember Objects and manually declare dependencies for computed properties.

- Vue's template syntax harnesses the full power of JavaScript expressions, while Handlebars' expression and helper syntax is intentionally quite limited in comparison.

- Performance-wise, Vue outperforms Ember [by a fair margin](http://www.stefankrause.net/js-frameworks-benchmark7/table.html), even after the latest Glimmer engine update in Ember 2.x. Vue automatically batches updates, while in Ember you need to manually manage run loops in performance-critical situations.

## Knockout

Knockout was a pioneer in the MVVM and dependency tracking spaces and its reactivity system is very similar to Vue's. Its [browser support](http://knockoutjs.com/documentation/browser-support.html) is also very impressive considering everything it does, with support back to IE6! Vue on the other hand only supports IE9+.

Over time though, Knockout development has slowed and it's begun to show its age a little. For example, its component system lacks a full set of lifecycle hooks and although it's a very common use case, the interface for passing children to a component feels a little clunky compared to [Vue's](components.html#Content-Distribution-with-Slots).

There also seem to be philosophical differences in the API design which if you're curious, can be demonstrated by how each handles the creation of a [simple todo list](https://gist.github.com/chrisvfritz/9e5f2d6826af00fcbace7be8f6dccb89). It's definitely somewhat subjective, but many consider Vue's API to be less complex and better structured.

## Polymer

Polymer is another Google-sponsored project and in fact was a source of inspiration for Vue as well. Vue's components can be loosely compared to Polymer's custom elements and both provide a very similar development style. The biggest difference is that Polymer is built upon the latest Web Components features and requires non-trivial polyfills to work (with degraded performance) in browsers that don't support those features natively. In contrast, Vue works without any dependencies or polyfills down to IE9.

In Polymer, the team has also made its data-binding system very limited in order to compensate for the performance. For example, the only expressions supported in Polymer templates are boolean negation and single method calls. Its computed property implementation is also not very flexible.

## Riot

Riot 3.0 provides a similar component-based development model (which is called a "tag" in Riot), with a minimal and beautifully designed API. Riot and Vue probably share a lot in design philosophies. However, despite being a bit heavier than Riot, Vue does offer some significant advantages:

- Better performance. Riot [traverses a DOM tree](http://riotjs.com/compare/#virtual-dom-vs-expressions-binding) rather than using a virtual DOM, so suffers from the same performance issues as AngularJS.
- More mature tooling support. Vue provides official support for [webpack](https://github.com/vuejs/vue-loader) and [Browserify](https://github.com/vuejs/vueify), while Riot relies on community support for build system integration.
