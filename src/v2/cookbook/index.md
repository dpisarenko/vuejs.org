---
title: Einführung
type: cookbook
order: 0
---

## Das Kochbuch und die Anleitung

Wie unterscheidet sich das Kochbuch von der Anleitung? Warum ist das notwendig?

* **Bessere Fokussierung**: In der Anleitung erzählen wir eine Geschichte. Jeder Abschnitt baut auf dem vorigen auf und setzt voraus, dass der Leser das Wissen des vorigen Abschnitts verinnerlicht hat. Im Kochbuch kann und soll jedes Rezept für sich allein, isoliert stehen. Das bedeutet, dass die Rezepte sich jeweils ein spezifisches Aspekt von Vue konzentrieren können, statt einen allgemeinen Überblick zu geben.

* **Größere Tiefe**: Um zu verhindern, dass die Anleitung zu lang wird, versuchen wir immer die einfachsten möglichen Beispiele zu jeder Funktionalität zu inkludieren. Dann gehen wir weiter. Im Kochbuch können wir komplexere Beispiele inkludieren und Funktionalitäten miteinander kombinieren. Jedes Rezept kann so lang und so detailliert sein, wie es muss, um die jeweilige Niche vollständig zu beleuchten.

* **JavaScript lehren**: In der Anleitung setzen wir zumindest mittlere Vertrautheit mit ES5 JavaScript voraus. Zum Beispiel werden nicht erklären, wie `Array.prototype.filter` in einer berechneten Eigenschaft funktioniert, die eine Liste filtert. Im Kochbuch dagegen können grundlegende JavaScript-Funktionalitäten (inklusive ES6/2015+) weiter erforscht und im Kontext erklärt werden, wie sie uns helfen, bessere Vue-Anwendungen zu kreiern.

* **Das Ökosystem erforschen**: Bei fortgeschrittenen Funktionalitäten setzen wir eine gewisse Kenntnis des Ökosystems voraus. Zum Beispiel, wenn Du 1-Datei-Komponenten in Webpack verwenden willst, werden wir nicht erklären, wie Du nicht-Vue-Teile von Webpack konfigurieren sollst. Im Kochbuch haben wir den Raum, um diese Ökosystem-Bibliotheken tiefer zu erforschen - zumindest so weit, wie das für die Vue-Entwickler universell nützlich ist.

## Beiträge zum Kochbuch

### Was wir suchen

Das Kochbuch gibt Entwicklern Beispiele, auf denen sie aufbauen können. Diese beziehen sich sowohl auf häufige, als auch interessante Anwendungsfälle. Sie erklären die einzelnen Themen mit zunehmend komplexeren Details. Unser Ziel ist es, über einfache einführende Beispiele hinauszugehen und jene Konzepte zu zeigen, die weiter anwendbar sind, aber auch Vorbehalte gegen diese Ansätze aufzuzeigen.

Wenn Du einen beitragen willst, starte bitte die Zusammenarbeit indem Du ein Issue mit dem Tag **cookbook idea** mit Deinem Konzept einträgst. So können wir Dich zu einem erfolgreichen Pull Request begleiten. Nachdem Deine Idee genehmigt wurde, folge bitte der untenstehenden Vorlage soweit wie möglich. Einige Abschnitte sind obligatorisch, einige optional. Das Befolgen der numerischen Reihenfolge wird stark empfohlen, aber nicht benötigt.

Rezepte sollten im Allgemeinen:

> * Ein spezifisches, allgemeines Problem lösen
> * Mit einem kleinstmöglichen Beispiel beginnen
> * 

> * Introduce complexities one at a time
> * Link to other docs, rather than re-explaining concepts
> * Describe the problem, rather than assuming familiarity
> * Explain the process, rather than just the end result
> * Explain the pros and cons of your strategy, including when it is and isn't appropriate
> * Mention alternative solutions, if relevant, but leave in-depth explorations to a separate recipe

We request that you follow the template below. We understand, however, that there are times when you may necessarily need to deviate for clarity or flow. Either way, all recipes should at some point discuss the nuance of the choice made using this pattern, preferably in the form of the alternative patterns section.

### Base Example

_required_

1.  Articulate the problem in a sentence or two.
2.  Explain the simplest possible solution in a sentence or two.
3.  Show a small code sample.
4.  Explain what this accomplishes in a sentence.

### Details about the Value

_required_

1.  Address common questions that one might have while looking at the example. (Blockquotes are great for this)
2.  Show examples of common missteps and how they can be avoided.
3.  Show very simple code samples of good and bad patterns.
4.  Discuss why this may be a compelling pattern. Links for reference are not required but encouraged.

### Real-World Example

_required_

Demonstrate the code that would power a common or interesting use case, either by:

1.  Walking through a few terse examples of setup, or
2.  Embedding a codepen/jsfiddle example

If you choose to do the latter, you should still talk through what it is and does.

### Additional Context

_optional_

It's extremely helpful to write a bit about this pattern, where else it would apply, why it works well, and run through a bit of code as you do so or give people further reading materials here.

### When To Avoid This Pattern

_optional_

This section is not required, but heavily recommended. It won't make sense to write it for something very simple such as toggling classes based on state change, but for more advanced patterns like mixins it's vital. The answer to most questions about development is ["It depends!"](https://codepen.io/rachsmith/pen/YweZbG), this section embraces that. Here, we'll take an honest look at when the pattern is useful and when it should be avoided, or when something else makes more sense.

### Alternative Patterns

_required_

This section is required when you've provided the section above about avoidance. It's important to explore other methods so that people told that something is an antipattern in certain situations are not left wondering. In doing so, consider that the web is a big tent and that many people have different codebase structures and are solving different goals. Is the app large or small? Are they integrating Vue into an existing project, or are they building from scratch? Are their users only trying to achieve one goal or many? Is there a lot of asynchronous data? All of these concerns will impact alternative implementations. A good cookbook recipe gives developers this context.

## Thank you

It takes time to contribute to documentation, and if you spend the time to submit a PR to this section of our docs, you do so with our gratitude.
