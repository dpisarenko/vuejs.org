---
title: Vue-Komponenten für npm verpacken
type: cookbook
order: 12
---

## Grundlegendes Beispiel

Vue-Komponenten wurden von Anfang an für die Wiederverwendung konzipiert. Das ist einfach, wenn die Komponente nur innerhalb einer Anwendung verwendet wird. Aber wie kannst Du eine Komponente ein Mal schreiben und in mehreren Websites bzw. Anwendungen verwenden? Die vermutlich einfachste Lösung ist npm.

Wenn Du Deine Komponente für die Verteilung mit npm verpackst, kann sie in den Build-Prozess integriert werden:

```js
import MyComponent from 'my-component';

export default {
  components: {
    MyComponent,
  },
  // Hier kommt der Rest der Komponente
}
```

Sie kann dann auch mit dem `<script>`-Tag direkt in den Browser eingebunden werden:

```html
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/my-component"></script>
  ...
  <my-component></my-component>
  ...
```

Das vermeidet nicht nur die Code-Duplizierung, sondern erlaubt Dich auch, der Vue-Gemeinschaft etwas zurückzugeben!

## Kann ich nicht einfach die `.vue`-Dateien teilen?

Mit Vue ist es bereits möglich, Komponenten als eine einzige Datei zu schreiben. Weil eine Ein-Datei-Komponente (SFC, single file component) bereits aus nur einer Datei besteht, ist die Frage berechtigt:

> "Warum können die Leute meine `.vue`-Datei nicht direkt verwenden? Ist das nicht die einfachste Art, Komponenten zu teilen?"

Es stimmt, Du kannst `.vue`-Dateien direkt teilen, und jeder, der [Vue build](https://vuejs.org/v2/guide/installation.html#Explanation-of-Different-Builds) verwendet, in dem der Vue-Kompiler enthalten ist, kann sie auch unmittelbar konsumieren. Außerdem verwendet der SSR-Build Stringverkettung zur Optimierung, daher kann der Ansatz mit der `.vue`-Datei in diesem Fall besser geeignet sein (für Details siehe [Vue-Komponenten für npm verpacken > Verwendung von SSR](#SSR-Usage)). Jedoch schließt das all jene aus, die die Komponente direkt im Browser über das `<script>`-Tag einbinden wollen, all jene, die einen Runtime-only-Build verwenden, or Build-Prozesse, die nicht wissen, wie sie mit `.vue`-Dateien umgehen sollen.

Wenn Du Deine SFC für die Verteilung mit npm richtig verpackst, dann kannst Du sie so teilen, dass jeder sie überall verwenden kann!

## Komponenten für npm verpacken

Für die Zwecke dieses Abschnitts werden wir folgende Dateistruktur annehmen:

```
package.json
build/
   rollup.config.json
src/
   wrapper.js
   my-component.vue
dist/
```

<p class="tip">In diesem Dokument nehmen wir auf die Datei package.json Bezug, die oben aufgelisttet ist. Die Datei in diesen Beispielen wurde händisch erzeugt und wird eine minimale Konfiguration beinhalten, die für die Darlegung der jeweiligen Aufgabe benötigt wird. Es ist wahrscheinlich, dass Deine eigene package.json-Datei viel mehr beinhalten wird, als hier angegeben.</p>

### Wie weiss npm, welche Version im Browser bzw. Build-Prozess verwendet werden soll?

Es muss nur eine Version (`main`) in der Datei package.json angegeben sein. Wir können aber auch mehrere angeben. Wir können die zwei häufigsten Anwendungsfälle abdecken, indem wir zwei zusätzliche Versionen (`module` und `unpkg`) angeben und Zugang zur `.vue`-Datei selber mittels des `browser`-Feldes gewähren. Eine Beispiel für package.json würde so aussehen:

```json
{
  "name": "my-component",
  "version": "1.2.3",
  "main": "dist/my-component.umd.js",
  "module": "dist/my-component.esm.js",
  "unpkg": "dist/my-component.min.js",
  "browser": {
    "./sfc": "src/my-component.vue"
  },
  ...
}
```

Wenn Webpack 2+, Rollup, oder andere moderne Tools verwendet werden, werden sie auf dem `module`-Build aufbauen. Legacy-Anwendungen werden den `main`-Build, und der `unpkg`-Build kann direkt in einem Browser verwendet werden. Tatsächlich verwendet [unpkg](https://unpkg.com) CDN automatisch diese Version, wenn jemand die URL Deines Moduls in ihrem Service eingibt!

### Verwendung von SSR

Möglicherweise hast Du etwas Interessantes bemerkt -- Browser werden nicht die `browser`-Version verwenden. Das ist deswegen, weil dieses Feld eingeführt wurde, damit Autoren [Hinweise für Bundler](https://github.com/defunctzombie/package-browser-field-spec#spec) angeben können, die eigene Pakete für clientseitige Verwendung erstellen. Mit etwas Kreativität erlaubt uns dieses Feld, ein Alias zur `.vue`-Datei selber anzugeben. Zum Beispiel:

```js
import MyComponent from 'my-component/sfc'; // Beachte '/sfc'
```

Kompatible Bundler sehen die `browser`-Definition in package.json und übersetzen die Anfragen an `my-component/sfc` in `my-component/src/my-component.vue`. Dadurch wird die originale `.vue`-Datei benutzt. Jetzt kann der SSR-Prozess die Optimierungen mit Stringverkettung verwenden und damit die Performance verbessern.

<p class="tip">Beachte: Wenn Du `.vue`-Komponenten direkt verwendest, achte auf alle möglichen Typen der Präprozessoren, die von den `script`- und `style`-Tags benötigt wird. Diese Abhängigkeiten werden an die Benutzer weitergegeben. Erwäge, 'einfache' SFCs zu Verfügung zu stellen, um die Dinge so leichtgewichtig wie es geht zu halten.</p>

### Wie mache ich mehrere Versionen meiner Komponente?

Du brauchst Dein Modul nicht mehrmals zu schreiben. Es ist möglich, alle 3 Versionen Deines Moduls in einem Schritt und wenigen Sekunden zu erstellen. Das Beispiel hier verwendet [Rollup](https://rollupjs.org) aufgrund seiner minimalen Konfiguration. Ähnliche Konfigurationen sind mit anderen Build-Tools auch möglich. Details zu dieser Entscheidung kannst Du [hier](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c) finden. Im Abschnitt `scripts` in package.json muss man einen  Eintrag je Build-Ziel hinzufügen. Außerdem muss man ein generischeres `build`-Skript angeben, welches alle Build-Ziele in einem Lauf ausführt. Die Beispieldatei package.json schaut jetzt so aus:

```json
{
  "name": "my-component",
  "version": "1.2.3",
  "main": "dist/my-component.umd.js",
  "module": "dist/my-component.esm.js",
  "unpkg": "dist/my-component.min.js",
  "browser": {
    "./sfc": "src/my-component.vue"
  },
  "scripts": {
    "build": "npm run build:umd & npm run build:es & npm run build:unpkg",
    "build:umd": "rollup --config build/rollup.config.js --format umd --file dist/my-component.umd.js",
    "build:es": "rollup --config build/rollup.config.js --format es --file dist/my-component.esm.js",
    "build:unpkg": "rollup --config build/rollup.config.js --format iife --file dist/my-component.min.js"
  },
  "devDependencies": {
    "rollup": "^0.57.1",
    "rollup-plugin-buble": "^0.19.2",
    "rollup-plugin-vue": "^3.0.0",
    "vue": "^2.5.16",
    "vue-template-compiler": "^2.5.16",
    ...
  },
  ...
}
```

<p class="tip">Erinnere Dich daran, dass wenn Du eine existierende package.json-Datei hast, es wahrscheinlich viel mehr enthalten wird, als diese. Diese Datei illustriert lediglich einen Startpunkt. Außerdem sind die in devDependencies angeführten <i>Pakete</i> (nicht ihre Versionen) die minimalen Voraussetzungen dafür, dass Rollup die drei erwähnten, separaten Builds (umd, es und unkp) erstellen kann. Wenn neue Versionen herausgebracht werden, müssen sie entsprechend aktualisiert werden.</p>

Unsere Änderungen an package.json sind jetzt vollständig. Als Nächstes brauchen wir einen kleinen Wrapper, um die SFC zu exportieren bzw. automatisch zu installieren. Außerdem ist eine minimale Rollup-Konfiguration erforderlich. Danach sind wir fertig!

### Wie sieht meine verpackte Komponente aus?


Depending on how your component is being used, it needs to be exposed as either a [CommonJS/UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a) javascript module, an [ES6 javascript](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e) module, or in the case of a `<script>` tag, it will be automatically loaded into Vue via `Vue.use(...)` so it's immediately available to the page. This is accomplished by a simple wrapper.js file which handles the module export and auto-install. That wrapper, in its entirety, looks like this:

```js
// Import vue component
import component from './my-component.vue';

// Declare install function executed by Vue.use()
export function install(Vue) {
	if (install.installed) return;
	install.installed = true;
	Vue.component('MyComponent', component);
}

// Create module definition for Vue.use()
const plugin = {
	install,
};

// Auto-install when vue is found (eg. in browser via <script> tag)
let GlobalVue = null;
if (typeof window !== 'undefined') {
	GlobalVue = window.Vue;
} else if (typeof global !== 'undefined') {
	GlobalVue = global.Vue;
}
if (GlobalVue) {
	GlobalVue.use(plugin);
}

// To allow use as module (npm/webpack/etc.) export component
export default component;
```

Notice the first line directly imports your SFC, and the last line exports it unchanged. As indicated by the comments in the rest of the code, the wrapper provides an `install` function for Vue, then attempts to detect Vue and automatically install the component. With 90% of the work done, it's time to sprint to the finish!

### How do I configure the Rollup build?

With the package.json `scripts` section ready and the SFC wrapper in place, all that is left is to ensure Rollup is properly configured. Fortunately, this can be done with a small 16 line rollup.config.js file:

```js
import vue from 'rollup-plugin-vue'; // Handle .vue SFC files
import buble from 'rollup-plugin-buble'; // Transpile/polyfill with reasonable browser support
export default {
    input: 'build/wrapper.js', // Path relative to package.json
    output: {
        name: 'MyComponent',
        exports: 'named',
    },
    plugins: [
        vue({
            css: true, // Dynamically inject css as a <style> tag
            compileTemplate: true, // Explicitly convert template to render function
        }),
        buble(), // Transpile to ES5
    ],
};
```

This sample config file contains the minimum settings to package your SFC for npm. There is room for customization, such as extracting CSS to a separate file, using a CSS preprocessor, uglifying the JS output, etc.

Also, it is worth noting the `name` given the component here. This is a PascalCase name that the component will be given, and should correspond with the kebab-case name used elsewhere throughout this recipe.

### Will this replace my current development process?

The configuration here is not meant to replace the development process that you currently use. If you currently have a webpack setup with hot module reloading (HMR), keep using it! If you're starting from scratch, feel free to install [Vue CLI 3](https://github.com/vuejs/vue-cli/), which will give you the whole HMR experience config free:

```bash
vue serve --open src/my-component.vue
```

In other words, do all of your development in whatever way you are comfortable. The things outlined in this recipe are more like 'finishing touches' than a full dev process.

## When to Avoid this Pattern

Packaging SFCs in this manner might not be a good idea in certain scenarios. This recipe doesn't go into detail on how the components themselves are written. Some components might provide side effects like directives, or extend other libraries with additional functionality. In those cases, you will need to evaluate whether or not the changes required to this recipe are too extensive.

In addition, pay attention to any dependencies that your SFC might have. For example, if you require a third party library for sorting or communication with an API, Rollup might roll those packages into the final code if not properly configured. To continue using this recipe, you would need to configure Rollup to exclude those files from the output, then update your documentation to inform your users about these dependencies.

## Alternative Patterns

At the time this recipe was written, Vue CLI 3 was itself in beta. This version of the CLI comes with a built-in `library` build mode, which creates CommonJS and UMD versions of a component. This might be adequate for your use cases, though you will still need to make sure your package.json file points to `main` and `unpkg` properly. Also, there will be no ES6 `module` output unless that capability is added to the CLI before its release or via plugin.

## Acknowledgements

This recipe is the result of a lightning talk given by [Mike Dodge](https://twitter.com/mgdodgeycode) at VueConf.us in March 2018. He has published a utility to npm which will quickly scaffold a sample SFC using this recipe. You can download the utility, [vue-sfc-rollup](https://www.npmjs.com/package/vue-sfc-rollup), from npm. You can also [clone the repo](https://github.com/team-innovation/vue-sfc-rollup) and customize it.
