---
title: vue-cli ankündigen
date: 2015-12-28 00:00:00
---

In letzter Zeit wurde oft über die [Hürden im Bezug auf Tooling diskutiert](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.chg95e5p6) beim Beginn eines React-Projekts. Glücklicherweise für Vue.js, brauchst du nur es nur in einem `<script>` Tag von einem CDN einzubinden um mit einem schnellen Prototypen loslegen zu können. Jedoch ist das nicht wie du eine professionelle App entwickeln würdest. Dafür wird bestimmtes Tooling benötigt für Modularisierung, Transpiler, Präprozessoren, Hot-Reload, Linting und Testing. Diese Werkzeuge werden benötigt um die Langzeit-Wartbarkeit eines großen Projekts gewährleisten zu können. Deren Setup kann allerdings große Probleme bereiten. Das ist es wesshalb wir hier das [vue-cli](https://github.com/vuejs/vue-cli) vorstellen wollen, eine einfache CLI-Anwendung, welche dir helfen wird ein Vue.js Projekt schnell auf die Beine zu stellen mit meinungsstarken, getesteten Build-Setups. 

<!-- more -->

### Nur das Gerüst

Der Gebrauch sieht wie folgt aus:

``` bash
npm install -g vue-cli
vue init webpack my-project
# Beantworte die Prompts
cd my-project
npm install
npm run dev # tada!
```

Die CLI macht nicht mehr als Templates von der [vuejs-templates Organisation](https://github.com/vuejs-templates) zu ziehen. Abhängigkeiten werden mit NPM gehandelt und Build-Tasks sind einfache NPM-Skripts.

### Offizielle Templats

Der Zweck der offiziellen Vue-Templates ist es Setups, welche meinungsstark sind und inklusive Tests zur Verfügung zu stellen, sodass User mit der Entwicklung der eigentlichen App so schnell wie möglich loslegen können. Diese Setups schreiben jedoch nicht vor, wie die App selbst strukturiert werden soll oder welche zusätzlichen Bibliotheken eingesetzt werden.

Alle offiziellen Templates sind Repos der [vuejs-templates Organisation](https://github.com/vuejs-templates). Wenn ein neues Template hinzugefügt wird, wirst du mit `vue init <template-name> <project-name>` dieses nutzen können. Du kannst auch mit `vue list` alle offiziell erhältlichen Templates sehen.

Derzeit sind unter anderem verfügbar:

- [browserify](https://github.com/vuejs-templates/browserify) - Ein voll ausgestattetes Browserify + vueify Setup mit Hot-Reload, Linting und Unit-Testing.

- [browserify-simple](https://github.com/vuejs-templates/browserify-simple) - Ein einnfachen Browserify + vueify Setup für schnelle Prototypen.

- [webpack](https://github.com/vuejs-templates/webpack) - Ein voll ausgestattetes Webpack + vue-loader Setup mit Hot-Reloading, Linting, Testing und CSS-Extrahierung.

- [webpack-simple](https://github.com/vuejs-templates/webpack-simple) - Ein einfaches Webpack + vue-loader Setup für schnelle Prototypen.

### Bring dein eigenes Setup

Wenn du mit den offiziellen Templates unzufrieden bist, kannst du diese forken, an deine Bedürfnisse anpassen (oder von Grund auf dein eigenes erstellen) und es ebenfalls via `vue-cli` benutzen, weil `vue-cli` direkt mit GitHub Repos arbeiten kann:

``` bash
vue init username/repo my-project
```

### Vue-Komponenten überall

Verschiedene Templates dienen verschiedenen Vorhaben: Einfache Setups für schnelle Prototypen und voll ausgestattete Setups für ambitionierte Apps. Was jedoch alle gemeinsam haben ist, dass sie alle `*.vue` 1-Datei-Komponenten unterstützen. Das hat zur Folge, dass valide `*.vue` Komponenten von Drittanbietern einfach über NPM verteilt werden können - Lasst uns mehr wiederverwendbarer Komponenten erzeugen!
