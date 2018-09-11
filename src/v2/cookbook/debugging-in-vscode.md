---
title: Debuggen in VS Code und Chrome
type: cookbook
order: 8
---

In jedem Projekt wird es früher oder später notwendig zu verstehen, warum ein Fehler, gross oder klein, auftritt. In diesem Rezept werden wir einige Workflows für Benutzer untersuchen, die in VS Code programmieren und Chrome zum Testen verwenden.

Dieses Kochrezept zeigt, wie Sie die Chrome-Erweiterung [Debugger for Chrome](https://github.com/Microsoft/VSCode-chrome-debug) in Verbindung mit VS Code verwenden können, um Vue.JS-Anwendungen, die mit [Vue CLI](https://github.com/vuejs/vue-cli) erstellt wurden, zu debuggen.

## Voraussetzungen

Sie müssen Chrome und VS Code installiert haben. Stellen Sie sicher, dass die aktuelle Version der Erweiterung [Debugger für Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) in VS Code installiert ist.

Installieren und erstellen Sie ein Projekt mittels [vue-cli](https://github.com/vuejs/vue-cli). Die Anleitung zum Installieren befindet sich in der README-Datei des Projekts. Wechseln Sie in das neu erstellte Anwendungsverzeichnis und öffnen Sie VS Code.

### Quellcode in Chrome Devtools anzeigen

Bevor Sie Ihre Vue-Komponenten von VS Code aus debuggen können, müssen Sie die generierte Webpack-Konfiguration aktualisieren, damit Sourcemaps erstellt werden. Wir machen das, damit unser Debugger weiß, welchen Codeteilen in einer komprimierten Datei welche Originaldateien entsprechen. Dies stellt sicher, dass Sie auch dann eine Anwendung debuggen können, wenn Ihre Assets durch WebPack optimiert wurden.

Öffnen Sie `config/index.js` und finden Sie dort die Eigenschaft `devtool`. Verändern Sie sie, dass die Zeile so aussieht:

```json
devtool: 'source-map',
```

In Vue CLI 3 müssen Sie die `devtool`-Eigenschaft in `vue.config.js` setzen:

```js
module.exports = {
  configureWebpack: {
    devtool: 'source-map'
  }
}
```

### Anwendung aus VS Code heraus starten

Klicken Sie auf das Debugger-Icon in der Activity-Leiste, um die Debug-Ansicht einzublenden. Dann klicken Sie auf das Zahnrad-Icon, um eine `launch.json`-Datei zu konfigurieren. Wählen Sie dabei **Chrome** als Umgebung aus. Ersetzen Sie den Inhalt der generierten `launch.json`-Datei mit den folgenden zwei Konfigurationen:

![Add Chrome Configuration](/images/config_add.png)

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "vuejs: chrome",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "breakOnLoad": true,
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```

## Einen Haltepunkt setzen

1.  Setzen Sie einen Haltepunkt in **src/components/HelloWorld.vue** auf `Zeile 90`, wo die `data`-Funktion einen Text zurückgibt.

  ![Renderer des Haltepunkts](/images/breakpoint_set.png)

2.  Öffnen Sie Ihr Lieblingsterminal im Wurzelverzeichnis und starten Sie die Anwendung mit der Vue CLI:

  ```
  npm start
  ```

3.  Gehen Sie in die Debug-Ansicht und wählen Sie dort die **'vuejs: chrome'**-Konfiguration aus. Drücken Sie dann F5 oder klicken Sie auf den grünen Play-Button.

4.  Wenn die neue Instanz der Anwendung in Chrome unter `http://localhost:8080` geöffnet wird, sollte die Ausführung an Ihrem Haltepunkt stehenbleiben.

  ![Haltepunkt wurde getroffen](/images/breakpoint_hit.png)

## Alternative Muster

### Vue Devtools

Es gibt andere Debug-Methoden unterschiedlicher Komplexität. Die beliebteste und einfachste davon ist [vue-devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd). Einer der Vorteile ist dass sie es erlauben, die Daten-Eigenschaften live zu verändern und die Veränderungen sofort zu sehen. Der andere wichtige Vorteil ist die Fähigkeit der "Zeitreisen" beim Debuggen von Vuex.

![Zeitreisender Debugger von devtools](/images/devtools-timetravel.gif)

<p class="tip">Bitte beachten Sie, dass wenn die Seite einen Produktions- bzw. minifizierten Build von Vue.js verwendet (wie jener auf dem Standard-Link vom CDN), die devtools-Inspektion per Voreinstellung ausgeschaltet ist. Deswegen wird der Vue-Abschnitt nicht sichtbar sein. Wenn Sie zur nicht-minifizierten Version umschalten, könnte es notwendig sein, die Seit "hart" zu aktualisieren, damit die Veränderungen sichtbar werden.</p>

### Vuetron

[Vuetron](http://vuetron.io/) ist ein sehr nettes Projekt, das die Funktionalität von vue-devtools erweitert. Zusätzlich zum normalen devtools-Workflow können Sie:

* Schnell die API-Anfragen und -Antworten betrachten: Wenn Sie die Fetch API für die Anfragen verwenden, werden Ereignisse für alle abgeschickten Anfragen dargestellt. Die ausgerollten Kartenansicht enthält sowohl die Anfrage- als auch die Antwort-Daten.
* Abonnieren von spezifischen Teilen des Zustands Ihrer Anwendung, um das Debuggen zu beschleunigen
* Visualisieren der Komponentenhierarchie. Eine Animation erlaubt Ihnen, den Baum ein- und auszuklappen, um besondere Hierarchie-Ansichten zu haben.

![Hierarchie von Vuetron](/images/vuetron-heirarchy.gif)

### Einfacher Debug-Statement

The example above has a great workflow. However, there is an alternative option where you can use the [native debugger statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger) directly in your code. If you choose to work this way, it's important that you remember to remove the statements when you're done.

```js
<script>
export default {
  data() {
    return {
      message: ''
    }
  },
  mounted() {
    const hello = 'Hello World!'
    debugger
    this.message = hello
  }
};
</script>
```

## Acknowledgements

This recipe was based on a contribution from [Kenneth Auchenberg](https://twitter.com/auchenberg), [available here](https://github.com/Microsoft/VSCode-recipes/tree/master/vuejs-cli).
