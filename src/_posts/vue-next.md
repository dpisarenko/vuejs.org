---
title: Vue.js 0.10.6, und was als nächstes kommt
date: 2014-07-29 00:04:55
---

## 0.10.6

Vue.js 0.10.6 wurde veröffentlicht! Dies ist ein weiteres kleines Bugfix und wird die letzte Wartungsversion bleiben bis zur nächsten großen Hauptversion.

<!-- more -->

- `v-style` Fehler wenn Wert ist fehlerhaft oder eine Zahl behoben. ( Danke an [@dmfilipenko](https://github.com/dmfilipenko) )
- Fehler mit `currency` Filter wenn Wert ist ein String behoben ( Danke an [@dmfilipenko](https://github.com/dmfilipenko) )
- `Vue.require` zum Bauen von Komponenten v1.0+ gefixt ( Danke an [@kewah](https://github.com/kewah) )
- Template-Knoten erlauben als Template-Optionen übergeben zu werden ( Danke an [@jordangarcia](https://github.com/jordangarcia) )
- `vm.$destroy()` akzeotiert nun ein optionales Argument `noRemove`. Wenn als `true` übergeben, wird es vm's DOM Knoten, nachdem das vm gelöscht wurde, intakt lassen.

## Vue-next

Mache von euch werden die [next](https://github.com/yyx990803/vue/tree/next) Branch in der Repo bemerkt haben. Es stimmt, ich schreibe Vue.js von Grund auf neu. Es gibt zwei Gründe für diese Enscheidung:

- Probleme lösen die auf Designfehlern der aktuellen Version beruhen. Diese Änderungen betreffen das Design einiger Kernmodule und sind damit einfacher neu zu schreiben als auf die aktuelle Codebase anzuwenden.
- Code-Qualty insgesamt verbessern ( insbesondere `compiler.js` ist derzeit ein großer Haufen Mist und Kommentare sind nicht konsistent )

Die `next` Branch steht noch **ganz am Angang**. Die Interna werden sich stark ändern und wenn es rauskommt wird das aktuelle Apps **brechen**. Trotzdem werde ich versuchen API-Änderungen zu vermeiden. Wesentliche Unterschiede zu der 0.10 Branch werden in `changes.md` festgehalten. Diese Liste ist natürlich nicht vollständig und wird sich ändern. Manches davon sind nur Ideen, aber es soll euch zeigen was zu erwarten ist und eurer Feedback zu jedem dieser Punkte ist willkommen.

Teile deine Meinung unter [vuejs/Discussion](https://github.com/vuejs/Discussion/issues).
