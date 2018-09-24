---
title: Verwendung von Axios zum Konsumieren von APIs
type: cookbook
order: 9
---

## Grundlegendes Beispiel

Es kommt oft vor, dass Du eine Web-Anwendung baust, die Daten einer API konsumiert und ausgibt. Es gibt mehrere Arten, wie man das tun kann. Ein sehr beliebter Ansatz ist [axios](https://github.com/axios/axios), ein promise-basierter HTTP-Client.

In diesem Beispiel werden wir die [CoinDesk-API](https://www.coindesk.com/api/) verwenden, um Bitcoin-Preise darzustellen, die jede Minute aktualisiert werden. Zuerst installieren wir axios, entweder mit npm/yarn or mittels eines CDN-Links.

Es gibt mehrere Arten, Informationen aus einem API anzufordern, aber es wäre nett, wenn wir zuerst sehen könnten, in welcher Form die Daten ankommen. Dann werden wir wissen, was genau wir ausgeben wollen. Um dies zu tun, werden wir eine Anfrage an ein API-Endpunkt senden und die Antwort ausgeben, sodass wir sie sehen können. Wir können in der Dokumentation des CoinDesk-API sehen, dass diese Anfrage an `https://api.coindesk.com/v1/bpi/currentprice.json` gerichtet wird. Zunächst erstllen wir eine Daten-Eigenschaft, die letzten Endes unsere Informationen beinhalten wird. Wir werden die Daten holen und zuweisen unter Verwendung des `mounted`-Lebenszyklushooks.

```js
new Vue({
  el: '#app',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => (this.info = response))
  }
})
```

```html
<div id="app">
  {{ info }}
</div>
```

Wir bekommen das:

<p data-height="350" data-theme-id="32763" data-slug-hash="80043dfdb7b90f138f5585ade1a5286f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="First Step Axios and Vue" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/80043dfdb7b90f138f5585ade1a5286f/">First Step Axios and Vue</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Exzellent! Wir haben irgendwelche Daten. Aber momentan sehen sie ziemlich unordentlich aus. Daher wollen wir sie geordnet ausgeben und Fehlerbehandlung hinzufügen für den Fall, dass etwas nicht funktioniert oder länger als erwartet dauert.

## Realistisches Beispiel: Mit den Daten arbeiten

### Daten aus dem API ausgeben

Es ist typisch, dass die Informationen, die wir brauchen, sich in der Antwort befinden. Wir müssen diese Daten durchgehen, um auf sie korrekt zuzugreifen. In unserem Fall sehen wir, dass die Preisinformationen sich in `response.data.bpi` befinden. Wenn wir das folgende Stück Code verwenden, wird die Ausgabe wie folgt aussehen.

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
```

<p data-height="200" data-theme-id="32763" data-slug-hash="6100b10f1b4ac2961208643560ba7d11" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Second Step Axios and Vue" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/6100b10f1b4ac2961208643560ba7d11/">Second Step Axios and Vue</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Das ist viel einfacher auszugeben, daher können wir unseren HTML-Code so verändern, dass nur die benötigten Informationen aus den Daten dargestellt werden, die wir empfangen. Wir werden einen [Filter](../api/#Vue-filter) kreiern, um sicherzustellen, dass sich der Dezimalpunkt an der richtigen Stelle befindet.

```html
<div id="app">
  <h1>Bitcoin Price Index</h1>
  <div
    v-for="currency in info"
    class="currency"
  >
    {{ currency.description }}:
    <span class="lighten">
      <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
    </span>
  </div>
</div>
```

```js
filters: {
  currencydecimal (value) {
    return value.toFixed(2)
  }
},
```

<p data-height="300" data-theme-id="32763" data-slug-hash="9d59319c09eaccfaf35d9e9f11990f0f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Third Step Axios and Vue" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/9d59319c09eaccfaf35d9e9f11990f0f/">Third Step Axios and Vue</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Fehler behandeln

Es kann vorkommen, dass wir keine Daten von der API bekommen. Unser Axios-Aufruf kann aus mehreren Gründen fehlschlagen, unter Anderem:

* Der Server, auf dem die API läuft, ist nicht verfügbar.
* Es wurde eine falsche Abfrage abgesendet.
* Die API gibt uns die Daten in einem anderen Format, als wir erwarten.

Wenn wir die Anfrage machen, sollten wir prüfen, ob solche Umstände vorliegen und entscheiden, was wir in jedem der Fälle tun. In einem axios-Aufruf machen wir das mittels eines `catch`-Blocks.

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
  .catch(error => console.log(error))
```

Damit werden wir wissen, ob während des API-Aufrufs etwas schief gelaufen ist oder nicht. Aber was ist, wenn die Daten verstümmelt sind oder die API down? Momentan wird der Benutzer einfach nichts sehen. Wir wollen einen Lader für diesen Fall bauen und dem Benutzer sagen, dass wir die Daten nicht holen konnten.

```js
new Vue({
  el: '#app',
  data () {
    return {
      info: null,
      loading: true,
      errored: false
    }
  },
  filters: {
    currencydecimal (value) {
      return value.toFixed(2)
    }
  },
  mounted () {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => {
        this.info = response.data.bpi
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
  }
})
```

```html
<div id="app">
  <h1>Bitcoin Price Index</h1>

  <section v-if="errored">
    <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
  </section>

  <section v-else>
    <div v-if="loading">Loading...</div>

    <div
      v-else
      v-for="currency in info"
      class="currency"
    >
      {{ currency.description }}:
      <span class="lighten">
        <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
      </span>
    </div>

  </section>
</div>
```

Du kannst auch auf die Rerun-Schaltfläche auf diesem Pen drücken, um den "Loading"-Status kurz zu sehen, während wir die Daten aus dem API holen:

<p data-height="300" data-theme-id="32763" data-slug-hash="6c01922c9af3883890fd7393e8147ec4" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Fourth Step Axios and Vue" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/6c01922c9af3883890fd7393e8147ec4/">Fourth Step Axios and Vue</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Das kann durch die Verwendung von Komponenten für unterschiedliche Abschnitte, sowie eine feiner granulierte Fehlerbehandlung noch weiter verbessert werden. Ob dies angemessen ist, hängt von der API und der Komplexität Deiner Anwendung ab.

## Alternative Muster

### Fetch API

Die [Fetch-API](https://developers.google.com/web/updates/2015/03/introduction-to-fetch) ist eine leistungsfähige, native API für diese Art von Anfragen. Möglicherweise hast Du gehört, dass einer der Vorteile der Fetch-API die Tatsache ist, dass Du keine externe Ressource laden musst, um sie zu verwenden. Das stimmt auch! Abgesehen davon, dass... es noch nicht vollständig unterstützt ist und Du deswegen ein polyfill benötigen wirst. Es gibt aber auch einige Fallen bei der Arbeit mit dieser API. Daher ziehen heute viele Axios vor. Jedoch kann sich das in Zukunft ändern.

Wenn Dich Fetch API interessiert, gibt es einige [sehr gute Artikel](https://scotch.io/@bedakb/lets-build-type-ahead-component-with-vuejs-2-and-fetch-api), wo erklärt wird, wie man sie verwendet.

## Abschluss

Abgesehen vom Konsumieren der API und der Ausgabe der Daten aus APIs, gibt es viele Arten mit Vue und axios zu arbeiten. Du kannst auch mit serverlosen Funktionen kommunizieren, bei APIs mit Schreibzugriff Daten hinzufügen/bearbeiten/löschen, und viele andere Dinge tun. Weil die Integration dieser beiden Bibliotheken so einfach ist, wurde dies zu einer sehr häufigen Wahl für Entwickler, die HTTP-Clients in ihren Arbeitsablauf integrieren müssen.