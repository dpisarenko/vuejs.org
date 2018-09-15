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
> * Eine Komplexität nach der anderen einführen (nicht mehrere auf einmal)
> * Links zu anderen Dokumenten einfügen statt die Konzepte neu zu erklären
> * Das Problem beschreiben statt vorauszusetzen, dass der Leser das Problem bereits kennt
> * Den Prozess erklären, statt nur das Endergebnis
> * Die Vor- und Nachteile Deiner Strategie erklären, inklusive der Fälle, wenn sie angemessen und nicht angemessen ist
> * Erwähne gegebenenfalls alternative Lösungen, aber schreibe detaillierte Ausführungen in einem separaten Kochrezept

Wir verlagen, dass Du der Vorlage unten folgst. Wir verstehen jedoch, dass es manchmal notwendig ist, davon abzuweichen, um die Klarheit oder den Textfluss zu verbessern. Wie auch immer, alle Rezepte sollten irgendwo auf die Nuancen der jeweils gewählten Lösung eingehen, am Besten in Form eines Abschnitts über alternative Muster.

### Grundlegendes Beispiel

_required_

1.  Beschreibe das Problem in 1-2 Sätzen.
2.  Erkläre die einfachste Lösung in 1-2 Sätzen.
3.  Zeige ein kleines Code-Beispiel.
4.  Erkläre in einem Satz, was dieser Code bewerkstelligt.

### Details über den Wert

_required_

1.  Spreche allgemeine Fragen an, die man beim Anschauen des Beispiel stellen könnte (Zitatblöcke sind perfekt dafür).
2.  Zeige Beispiele von häufigen Fehlern und wie sie vermieden werden können.
3.  Zeige sehr einfache Beispiele von guten und schlechten Mustern.
4.  Beschreibe, warum dies ein gutes Muster sein kann. Links zu Nachschlagewerken werden nicht verlangt, sondern empfohlen.

### Beispiel aus der realen Welt

_required_

Demonstriere den Code, welches in einem häufigen oder interessanten Anwendungsfall verwendet werden würde. Dabei gibt es zwei Möglichkeiten:

1.  Durchgehen von einigen wenigen, prägnanten Beispielen, oder
2.  Einbetten eines codepen/jsfiddle-Beispiels.

Wenn Du Dich für das Letztere entscheidest, solltest Du trotzdem beschreiben, was es ist und was es tut.

### Zusätzlicher Kontext

_optional_

Es ist extrem hilfreich ein wenig über dieses Muster zu schreiben: Wo kann es sonst angewandt werden? Warum es gut funktioniert? Dabei ist es gut, wenn Du beim Antworten durch Codebeispiele gehst oder dem Leser Hinweise auf weiterführende Texte gibst.

### Wann dieses Muster vermieden werden soll

_optional_

Dieser Abschnitt ist nicht erforderlich, aber stark empfohlen. Es macht keinen Sinn, einen solchen Abschnitt über derart simple Sachen wie das Umschalten von Klassen bei der Veränderung des Zustands zu schreiben. Für fortgeschrittene Muster wie Mixins ist er jedoch unerlässlich. Die Antwort auf die meisten Fragen über Software-Entwicklung lautet ["Hängt davon ab!"](https://codepen.io/rachsmith/pen/YweZbG). Dieser Abschnitt huldigt diesem Prinzip. Darin sehen wir uns ehrlich an, wann das Muster hilfreich ist, wann es vermieden werden woll, und wo etwas Anderes besser geeignet ist.

### Alternative Patterns

_required_

This section is required when you've provided the section above about avoidance. It's important to explore other methods so that people told that something is an antipattern in certain situations are not left wondering. In doing so, consider that the web is a big tent and that many people have different codebase structures and are solving different goals. Is the app large or small? Are they integrating Vue into an existing project, or are they building from scratch? Are their users only trying to achieve one goal or many? Is there a lot of asynchronous data? All of these concerns will impact alternative implementations. A good cookbook recipe gives developers this context.

## Thank you

It takes time to contribute to documentation, and if you spend the time to submit a PR to this section of our docs, you do so with our gratitude.
