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

Je nach dem, wie Deine Komponente verwendet wird, muss es entweder als ein [CommonJS/UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a) JavaScript-Modul, oder als [ES6-JavaScript](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e)-Modul freigegeben werden, oder, im Falle eines `<script>`-Tags, wird sie automatisch in Vue via `Vue.use(...)` geladen und steht dann unmittelbar auf der Seite zur Verfügung. Dies wird durch eine einfache wrapper.js-Datei bewerkstelligt, welche sich um den Export und die automatische Installation kümmert. Der gesamte Code des Wrappers sieht folgendermassen aus:

```js
// Importiere die Vue-Komponente aus './my-component.vue';
import component from './my-component.vue';

// Deklariere die Installationsfunktion, die von Vue.use() aufgerufen wird
export function install(Vue) {
	if (install.installed) return;
	install.installed = true;
	Vue.component('MyComponent', component);
}

// Erstelle die Moduldefinition für Vue.use()
const plugin = {
	install,
};

// Auto-Installation, wenn Vue gefunden wird (z. B. im Browser via <script>-Tag)
let GlobalVue = null;
if (typeof window !== 'undefined') {
	GlobalVue = window.Vue;
} else if (typeof global !== 'undefined') {
	GlobalVue = global.Vue;
}
if (GlobalVue) {
	GlobalVue.use(plugin);
}

// Wir brauchen das folgende Statement, um die Komponente als ein Modul (npm/webpack/etc.) verwenden zu können
export default component;
```

Beachte, dass die erste Zeile Dein SFC direkt importiert. Die letzte Zeile exportiert sie unverändert. Wie in den Kommentaren im übrigen Code angegeben, stellt der Wrapper eine `install`-Funktion für Vue zur Verfügung, dann versucht er, Vue zu entdecken und automatisch die Komponente zu installieren. 90% der Arbeit ist getan, es ist Zeit zur Ziellinie zu sprinten!

### Wie konfiguriere ich den Rollup-Build?

Wenn der Abschnitt `scripts` der package.json-Datei und der SFC-Wrapper fertig sind, dann bleibt uns nur sicherzustellen, dass Rollup richtig konfiguriert ist. Glücklicherweise können wir dies mit einer kleinen, 16-zeiligen rollup.config.js-Datei machen:

```js
import vue from 'rollup-plugin-vue'; // Behandle .vue-SFC-Dateien
import buble from 'rollup-plugin-buble'; // Transpiliere/polyfill mit einer vernünftigen Browser-Unterstützung
export default {
    input: 'build/wrapper.js', // Dieser Pfad ist relativ zu package.json
    output: {
        name: 'MyComponent',
        exports: 'named',
    },
    plugins: [
        vue({
            css: true, // Injiziere CSS dynamisch als <style>-Tag
            compileTemplate: true, // Wandle explizit Vorlagen zur Render-Funktionen
        }),
        buble(), // Transpiliere zu ES5
    ],
};
```

Diese Beispielkonfiguration enthält die minimalen Einstellungen, die notwendig sind, um Deine SFC für npm zu verpacken. Anpassungen wie  Extraktion von CSS in eine separate Datei, Verwendung von CSS-Präprozessor, die JS-Ausgabe verwirrden machen etc., sind möglich.

Beachte außerdem den Namen (`name`), der der Komponente vergeben wurde. Das ist ein Name in PascalCase, den die Komponente haben wird und sollte dem Namen in kebab-case entsprechen, der an anderen Stellen in diesem Rezept verwendet wurde.

### Wird dies meinen derzeit verwendeten Entwicklungsprozess ersetzen?

Das Ziel der hier angeführten Konfiguration ist es nicht, den Entwicklungsprozess zu ersetzen, den Du jetzt verwendest. Wenn Du momentan einen Webpack-Setup mit Hot Module Reloading (HMR) hast, verwende es weiter! Wenn Du von Null anfängst, kannst Du gerne [Vue CLI 3](https://github.com/vuejs/vue-cli/) installieren, welche Dir das ganze HMR-Erlebnis konfigurationsfrei geben wird:

```bash
vue serve --open src/my-component.vue
```

Mit anderen Worten: Entwickle so, wie Dir am bequemsten ist. Die hier besprochenen Dinge sind eher 'abschließende Striche' als ein vollständiger Entwicklungsprozess.

## Wann dieses Muster vermieden werden soll

In machen Fällen ist es keine gute Idee, SFC auf diese Weise zu verpacken. Dieses Rezept geht nicht im Detail darauf ein, wie die Komponenten geschrieben sind. Manche Komponenten können Nebenwirkungen wie Direktiven haben, oder andere Bibliotheken erweitern. In diesen Fällen musst Du evaluieren, ob sich die hier besprochenen Veränderungen in Deinem Fall auszahlen.

Zusätzlich beachte, welche Abhängigkeiten Dein SFC haben kann. Zum Beispiel, wenn Du eine externe Bibliothek zum Sortieren oder Kommunizieren mit einer API brauchst, kann Rollup diese Pakete in den finalen Code einpacken, wenn er nicht richtig konfiguriert ist. Um dieses Rezept weiterhin verwenden zu können, würdest Du Rollup konfigurieren müssen, sodass es diese Dateien aus der Ausgabe ausschließt. Dann würdest Du die Dokumentation aktualisieren und Deine Benutzer über diese Abhängigkeiten informieren.

## Alternative Muster

Zur Zeit, wenn dieses Rezept geschrieben wurde, befand sich Vue CLI 3 selbst in Beta. Diese Version von CLI wurde mit einem eingebauten Build-Modus `library` ausgeliefert, welcher CommonJS- und UMD-Versionen einer Komponente erstellt. Diese Option könnte für Deine Anwendungsfälle angemessen sein, obwohl Du dennoch sicherstellen musst, dass Deine package.json-Datei auf `main` und `unpkg` korrekt verweist. Es wird außerdem keine ES `module`-Ausgabe geben, es sei denn, diese Funktionalität wird zur CLI vor deren Herausgabe hinzugefügt (oder über einen Plugin).

## Danksagung

Dieses Rezept ist das Ergebnis eines Blitzvortrags von [Mike Dodge](https://twitter.com/mgdodgeycode) bei VueConf.us in März 2018. Er hat eine Utility für npm publiziert, die schnell das Gerüst eines Beispiel-SFC auf Basis dieses Rezepts machen kann. Du kannst dieses Programm, [vue-sfc-rollup](https://www.npmjs.com/package/vue-sfc-rollup), von npm herunterladen. Du kannst auch die [Repo klonen](https://github.com/team-innovation/vue-sfc-rollup) und anpassen.
