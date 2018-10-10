---
title: Vue.js 2.0 Ankündigung
date: 2016-04-27 13:33:00
---

Es freut mich heute das erste Preview von Vue.js 2.0 vorstellen zu können und damit viele neue Features und Verbesserungen. Lasst uns einen Blick darauf werfen!

<!-- more -->

## Noch schlanker, noch schneller

Vue.js war schon immer daran interessiert schlank und schnell zu bleiben, aber die 2.0 Version treibt das Ganze noch weiter. Die Rendering-Schicht basiert nun auf einer leichtgewichtigen Virtual-DOM Implementierung (basierend auf [Snabbdom](https://github.com/paldepind/snabbdom)) und verbessert damit das initiale Rendern und Speicherbedarf in den meisten Szenarien um 2~4x (schaut euch [die Benchmarks](https://github.com/vuejs/vue/tree/next/benchmarks) an). Der template-to-virtual-DOM Kompiler kann von der Laufzeit getrennt werden, sodass du Templates präkompilieren und allein mit der Laufzeitumgebung, welche kleiner als 12KB min+gzip (als Referenz, React 15 hat 44KB min+gzip) produktiv stellen kannst. Der Kompiler funktioniert auch im Browser, d.h. du kannst nach wie vor mit einem `<script>` Tag loslegen. Selbst mit dem Kompiler hat das Build nur 17KB min+gzip, immernoch kleiner als die aktuelle 1.0 Version. 

## Kein durchschnittliches Virtual-DOM

Virtual-DOM klingt etwas langweilig, weil es so viele Implementierungen davon git - das hier ist anders. Zusammen mit Vue's Reaktivitätssystem kann es ohne dein Zutun optimiertes Re-rendering bereitstellen. Jede Komponente verfolgt ihre reaktiven Abhängigkeiten wärend des Rendern und das System weiss genau wann etwas neu gerendert werden soll. `shouldComponentUpdate` und unveränderbare Datenstrukturen werden nicht länger benötigt - **es funktioniert einfach**.

Zusaätzlich werden in Vue 2.0 weiterführende Optimierungsschritte während der template-to-virtual-DOM Kompilierung durchgeführt:

1. Es werden statische Klassen und Attribute erkannt, sodass sie nach dem initialen Rendern niemals abgeändert werden.

2. Es wird der statische Sub-Trees (Sub-Trees ohne dynamische Verknüpfungen) erkannt und aus der Renderfunktion geworfen. Damit wird mit jedem Re-Render auf die selben virtuellen Knoten zugegriffen.

Diese weiterführende Optimierung kann meist nur mit Babel-Plugin umgesezt werden, wenn JSX eingesezt wird, aber mit Vue 2.0 kriegst du das selbst mit dem Browser-Kompiler hin.

Das neue Renderingsystem erlaubt es dir die reaktive Konvertierung auszustellen und gibt dir volle Kontrolle über den Re-Rendering-Prozess mit manuellen Updates.

Vue 2.0 kann mit diesen Techniken blitzschnelle Performanz in jedem Szenario und minimalen Aufwand deinerseits garantieren.

## Templates, JSX, oder Hyperscript?

Entwickler haben häufig starke Meinungen bezüglich Templates vs. JSX. Einerseits sind Templates näher an HTML - stimmen mit der semantischen Struktur deiner App überein und machen es einfacher üer das Desing, Layout und Styling nachzudenken. Andererseits sind Templates als DSL begrenzt während JSX/hyperscript alle Vorteile einer mächtigen, Turing-vollständigen Sprache bereitstellt.

Als Designer/Entwicker-Hybrid, bevorzuge ich Templates, aber in bestimmten Situationen vermisse ich die Flexibilität von JSX/hyperscript. Eine Komponente schreiben, die ihre Kinder programmatisch handelt ist ein Beispiel. Allein mit Slots in Template ist das nicht machbar.

Warum nicht beides? In Vue 2.0 kannst du mit der vertrauten Templatesyntax arbeiten oder auf die virtual-DOM Schicht wechseln, wann immer du dich durch die DSL eingeschrenkt fühlst. Statt der Option `template` kannst du einfach `render` benutzen. Du kannst sogar Renderfunktionen in deinem Template mit `<render>` nutzen! Das Beste von beiden in einem Framework.

## Server-seitiges Rendern streamen

Mit der Migrierung zu Virtual-DOM, Vue 2.0 kann Server-seitiges Rendern mit Client-seitiger Hydrierung unterstützen. Eine Schwierigkeit derzeitiger Implementierungen von Server-seitigem Rendern wie beispw. React ist, dass es synchron stattfindet und in koplexen Apps die Event-Loop auf dem Server blockieren kann. synchronisiertes, Server-seitiges Rendern kann sogar dazu führen das Inhalte auf dem Client langsamer angezeigt werden.Vue 2.0 bietet integriertes Streamen von Server-seitigem Rendern, sodass du deine Komponenten rendern, ein lesbaren Stream aufmachen und es direkt an die HTTP-Response übergeben kannst. Dies stellt sicher, dass dein Server ansprechbar bleibt und übergibt den gerenderten Inhalt an deine User schneller.

## Mehr Möglichkeiten aufschließen

Mit der neuen Architektur gibt es noch mehr zu erkunden - zu Beispiel Rendering zu nativen, mobilen Schnittstellen. Derzeit erkunden wir einen Port von Vue.js 2.0, der [weex](http://alibaba.github.io/weex/) als natives Backend für Rendering benuzt. Ein Projekt, dass von Ingenieuren der Alibaba Gruppe, dem größten IT-Unternehmen in China, gewartet wird. Es wäre auch möglich den Virtual-DOM von Vue 2.0 innerhalb ReactNative laufen zu lassen. Wir sind gespannt zu sehen was passiert!

## Kompatibilität und was als Nächstes zu erwarten ist

Vue.js 2.0 ist noch in Pre-Alpha, du kannst dir aber das Projekt [hier](https://github.com/vuejs/vue/tree/next/) anschauen. Trotz der Tatsache, dass es von Grund auf neu geschreiben worden ist, ist die API größtenteils kompatibel zu 1.0 mit einigen absichtichen Ausnahmen. Wenn du dir [die gleichen Beispiele in 2.0](https://github.com/vuejs/vue/tree/next/examples) ansiehst, wirst du feststellen, dass sich nicht viel geändert hat!

Die Abschreibung einiger Features ist Teil unserer Bemühungen eine möglichst einfache API für maximale Produktivität zur Verfügung zu stellen. Du findest eine Vergleichsliste der Features zwischen 1.0 und 2.0 [hier](https://github.com/vuejs/vue/wiki/2.0-features). Das bedeutet es wird Aufwand entstehen eine App zu migrieren, sollte sie einige dieser Features nutzen, aber wir werden in Zukunft detailierte Upgrade-Handbücher zur Verfügung stellen.

Es gibt noch immer viel zu tun. Wir werden eine erste Alpha veröffentlichen wenn wir ausreichende Tests haben und streben eine Beta Ende Mai / Anfang Juni an. Zusätzlich zu Tests, müssen wir auch die unterstüzenden Libraries aktualisieren (vue-router, Vuex, vue-loader, vueify, ...). Derzeit ist nur Vuex mit 2.0 kompatibel, aber wir werden dafür sorgen, dass alles zusammen funktioniert bevor wir 2.0 veröffentlichen.

Wir haben 1.x nicht vergessen! 1.1 wird parallel zu 2.0 Beta veröffentlicht mit einer LST-Periode von 6 Monatengeben für kritische Bugs und 9 Monate für Sicherheitsupdates. Es wird auch optionale Abschreibungswarnungen geben, sodass du dich auf 2.0 vorbereiten kannst. Bleib dran!
