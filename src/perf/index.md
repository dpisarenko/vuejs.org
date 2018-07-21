---
title: Performance-Vergleich
---

## TodoMVC Benchmark

> *Zuletzt aktualisiert am: 2014-10-12*

Suchen Sie das TodoMVC-Benchmark? Es wurde entfernt, weil wir mit einigen anderen Autoren des Frameworks gesprochen und uns darauf auf Folgendes geeinigt haben:

1. Die ursprüngliche Absicht dieser Benchmarks war es, die Performance der Browser, nicht der Frameworks, miteinander zu vergleichen. Der Test "löse Aktion xxx Mal synchron aus" spiegelt keine realistischen Benutzeraktionen wider.

2. Aufgrund der Unterschiede in der internen Implementierung, haben Frameworks, welche das asychrone Rendering verwenden (z. B. Vue, Om, Mercury), einen Vorteil gegenüber anderen, weil sie Teile der Berechnungen in der selben Ereignisschleife auslassen. In der realen Benutzererfahrung konnten derart hohe Unterschiede nicht festgestellt werden.

3. Im Allgemeinen hat diese Benchmark-Sammlung zu mehr Kontroverse als konstruktiven Einsichten   geführt. Daher wurde es entfernt und ich werde mich freuen, wenn ich sie durch etwas ersetzen kann, was die Frontend-Performance auf eine bessere Art und Weise misst.
