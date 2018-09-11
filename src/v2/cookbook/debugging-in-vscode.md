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

1.  Set a breakpoint in **src/components/HelloWorld.vue** on `line 90` where the `data` function returns a string.

  ![Breakpoint Renderer](/images/breakpoint_set.png)

2.  Open your favorite terminal at the root folder and serve the app using Vue CLI:

  ```
  npm start
  ```

3.  Go to the Debug view, select the **'vuejs: chrome'** configuration, then press F5 or click the green play button.

4.  Your breakpoint should now be hit as the new instance of Chrome opens `http://localhost:8080`.

  ![Breakpoint Hit](/images/breakpoint_hit.png)

## Alternative Patterns

### Vue Devtools

There are other methods of debugging, varying in complexity. The most popular and simple of which is to use the excellent [vue-devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd). Some of the benefits of working with the devtools are that they enable you to live-edit data properties and see the changes reflected immediately. The other major benefit is the ability to do time travel debugging for Vuex.

![Devtools Timetravel Debugger](/images/devtools-timetravel.gif)

<p class="tip">Please note that if the page uses a production/minified build of Vue.js (such as the standard link from a CDN), devtools inspection is disabled by default so the Vue pane won't show up. If you switch to an unminified version, you may have to give the page a hard refresh to see them.</p>

### Vuetron

[Vuetron](http://vuetron.io/) is a really nice project that extends some of the work that vue-devtools has done. In addition to the normal devtools workflow, you are able to:

* Quickly view API Request/Response: if you're using the fetch API for requests, this event is displayed for any request sent. The expanded card displays the request data as well as the response data.
* Subscribe to specific parts of your application’s state for faster debugging
* Visualize component hierarchy, and an animation allows you to collapse or expand the tree for specific hierarchy views.

![Vuetron Heirarchy](/images/vuetron-heirarchy.gif)

### Simple Debugger Statement

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
