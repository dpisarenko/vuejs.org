---
title: "Wesshalb Vue.js templateURL nicht unterstützt"
date: 2015-10-28 10:56:00
---

Eine häufig gestelle Fragen, besonders unter ehemaligen Angular-Nutzern, ist "Kann ich `templateURL` nutzen ?". Ich habe das so oft beantworten müssen, dass ich mir dachte etwas darüber zu schreiben.

<!-- more -->

In Angular, `templateURL` oder `ng-include` erlaubt es dem User ein Template dynamisch zur Laufzeit zu laden. Das scheint als eingebautes Feature auf den ersten Blick wirklich praktisch zu sein, aber lass uns überlegen welches Problem es zu lösen versucht.

Erstens, es ermöglicht uns unsere Templates in einer seperaten HTML-Datei zu schreiben. Das gibt uns korrektes Syntax-Highlighting, wesshalb es vermutlich viele bevorzugen. Aber ist es wirklich der beste Weg dein JavaScript-Code und das Template von einander zu trennen? Für eine Vue.js Komponente gilt, dass deren Template und JavaScript eng miteinander gekoppelt sind - die Handhabung ist in der Tat wesentlich einfacher wenn alles in einer Datei bleibt. Im Gegenteil, das Hin- und Herspringen zwischen zwei Dateien erschwert den Entwicklungsprozess. Konzeptuell sind Komponenten und nicht Templates die grundlegenden Bausteine einer Vue.js App. Jedes Vue.js Template gehört zu einem bestimmten JavaScript-Kontext - es gibt keinen Grund diese weiter aufzuteilen.

Zweitens, `templateURL` lädt das Template mit Ajax zur Laufzeit und du brauchst nicht einen zusätzlichen Build-Schritt um deine Dateien zu trennen. Dies ist vorteilhaft während der Entwicklung, kommt allerdingt mit ernsthaften Kosten in der Produktivumgebung. Solange HTTP/2 nicht universell unterstützt wird, bleibt die Anzahl der HTTP-Requests vermutlich entscheidend für die Initial-Load Performance deiner App. Stell dir vor du nutzt `templateURL` für jede Komponente - der Browser müsste duzende HTTP-Requests ausführen bevor überhaupt irgendwas angezeigt werden könnte! Du solltest wissen, dass die meisten Browser die Anzahl paralleler Requests zum selben Server begrenzen. Wenn du dieses Limit überschreitest, wird das initiale Rendern deiner App für jede zusätzliche Anfrage büßen müssen. Natürlich kann man mit bestimmten Build-Tools all die Templates in `$templateCache` vorregistrieren - aber das zeigt uns nur, dass ein zusätzlicher Build-Schritt unumgänglich ist.

Also, wie sollen wir ohne `templateURL` mit dem Problem umgehen, dass während des Entwicklungsprozesses auftritt? Inline-JavaScript String sind furchtbar, Templates vortäuschen mit `<script type="x/template">` fühlt sich auch nicht richtig an. Möglicherweise ist es nun an der Zeit auf ordentliche Module-Bundler einzusetzen wie [Webpack](http://webpack.github.io/) oder [Browserify](http://browserify.org/). Das mag einschüchternd sein, wenn man noch nie damit gearbeitet hat, aber der Mehraufwand ist es wert. Ordentliche Modularisierung ist Voraussetzung, wenn du etwas großes und wartbares bauen möchtest. Wichtiger ist, dass du [Vue-Komponenten in einer einzigen Datei](http://vuejs.org/guide/single-file-components.html) schreiben kannst, mit ordentlichem Syntax-Highlighting und den zusätzlichen Vorteilen von Präprozessoren, Hot-Reloading, ES2015 per Default, Autoprefixing und Scoped CSS, was deinen Entwicklungsprozess 10 Mal besser machen sollte.

Zuletzt, Vue erlaubt [Lazy-Loading deiner Komponenten](http://vuejs.org/guide/components.html#Async-Components) und mit Webpack ist die Umsetzung trivial. Allerdings sollte das nur dann eine Sorge sein, wenn dein initiales Bundle so groß ist, dass es besser ist es aufzusplitten.

Denk in Komponenten, nicht Templates.
