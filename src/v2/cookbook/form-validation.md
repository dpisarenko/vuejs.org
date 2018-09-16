---
title: Formularvalidierung
type: cookbook
order: 3
---

## Grundlegendes Beispiel

Formularvalidierung wird nativ vom Browser unterstützt. Manchmal handhaben bestimmte Browser dieses Thema so, dass man sich darauf nicht verlassen kann. Selbst wenn die Validierung perfekt unterstützt wird, können spezifische Anpassungen manchmal notwendig sein. In diesen Fällen kann eine manuellere, Vue-basierte besser geeignet sein. Beginnen wir mit einem einfachen Beispiel.

In einem Formular mit drei Feldern, mache zwei obligatorisch. Schauen wir uns zuerst den HTML-Code an:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
>

  <p v-if="errors.length">
    <b>Bitte korrigieren Sie den/die folgenden Fehler:</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <label for="age">Alter</label>
    <input
      id="age"
      v-model="age"
      type="number"
      name="age"
      min="0">
  </p>

  <p>
    <label for="movie">Lieblingsfilm</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

Gehen wir das Beispiel von oben nach unten durch. Das `<form>`-Tag hat eine ID, die wir für die Vue-Komponente verwenden werden. Es gibt einen Ereignishandler für die Schaltfläche "Abschicken", die Du gleich sehen wirst. `action` ist eine temporäre URL, die auf etwas Reales auf irgendeinem Server verweisen würde (wo Du natürlich eine serverseitige Backup-Validierung hast).

Darunter steht ein Absatz, das sich je nach Fehlerzustand ein- und ausblendet. Das wird eine einfache Liste von Fehlern im oberen Teil des Formulars rendern. Beachte auch, dass wir die Validierung beim Abschicken des Formulars auslösen und nicht bei Änderung eines Feldes.

Das Letzte, was Du beachten sollst, ist dass jedes der drei Felder ein zugehöriges `v-model` hat. Diese verbinden die Felder mit den Werten, mit denen wir in JavaScript arbeiten werden. Sehen wir uns das an.

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    age: null,
    movie: null
  },
  methods:{
    checkForm: function (e) {
      if (this.name && this.age) {
        return true;
      }

      this.errors = [];

      if (!this.name) {
        this.errors.push('Name ist ein Pflichtfeld.');
      }
      if (!this.age) {
        this.errors.push('Age ist ein Pflichtfeld.');
      }

      e.preventDefault();
    }
  }
})
```

Ziemlich kurz und einfach. Wir definieren ein Array, um die Fehler zu speichern und setzen die Werte dreier Felder auf `null`. Die `checkForm`-Logik (welche beim Abschicken des Formulars ausgeführt wird) prüft nur den Namen und das Alter, weil der Lieblingsfilm optional ist. Wenn sie leer sind, prüfen wir jedes davon und setzen einen spezifischen Fehler für jeden von ihnen. Und das war's. Du kannst das Demo unten laufen lassen. Vergiss nicht, dass beim erfolgreichen Abschicken, eine POST-Anfrage an eine temporäre URL geschickt wird.

<p data-height="265" data-theme-id="0" data-slug-hash="GObpZM" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 1" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/GObpZM/">form validation 1</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Verwenden benutzerdefinierter Validierung


For the second example, the second text field (age) was switched to email which will be validated with a bit of custom logic. The code is taken from the StackOverflow question, [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript). This is an awesome question because it makes your most intense Facebook political/religious argument look like a slight disagreement over who makes the best beer. Seriously - it's insane. Here is the HTML, even though it's really close to the first example.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
    <input
      id="name"
      v-model="name"
      type="text
      name="name"
    >
  </p>

  <p>
    <label for="email">Email</label>
    <input
      id="email"
      v-model="email"
      type="email"
      name="email"
    >
  </p>

  <p>
    <label for="movie">Favorite Movie</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

While the change here is small, note the `novalidate="true"` on top. This is important because the browser will attempt to validate the email address in the field when `type="email"`. Frankly it may make more sense to trust the browser in this case, but as we wanted an example with custom validation, we're disabling it. Here's the updated JavaScript.

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    email: null,
    movie: null
  },
  methods: {
    checkForm: function (e) {
      this.errors = [];

      if (!this.name) {
        this.errors.push("Name required.");
      }
      if (!this.email) {
        this.errors.push('Email required.');
      } else if (!this.validEmail(this.email)) {
        this.errors.push('Valid email required.');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    },
    validEmail: function (email) {
      var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    }
  }
})
```

As you can see, we've added `validEmail` as a new method and it is simply called from `checkForm`. You can play with this example here:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqNXZ" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqNXZ/">form validation 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Another Example of Custom Validation

For the third example, we've built something you've probably seen in survey apps. The user is asked to spend a "budget" for a set of features for a new Star Destroyer model. The total must equal 100. First, the HTML.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    Given a budget of 100 dollars, indicate how much
    you would spend on the following features for the
    next generation Star Destroyer. Your total must sum up to 100.
  </p>

  <p>
    <input
      v-model.number="weapons"
      type="number"
      name="weapons"
    > Weapons <br/>
    <input
      v-model.number="shields"
      type="number"
      name="shields"
    > Shields <br/>
    <input
      v-model.number="coffee"
      type="number"
      name="coffee"
    > Coffee <br/>
    <input
      v-model.number="ac"
      type="number"
      name="ac"
    > Air Conditioning <br/>
    <input
      v-model.number="mousedroids"
      type="number"
      name="mousedroids"
    > Mouse Droids <br/>
  </p>

  <p>
    Current Total: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

Note the set of inputs covering the five different features. Note the addition of `.number` to the `v-model` attribute. This tells Vue to cast the value to a number when you use it. However, there is a bug with this feature such that when the value is blank, it turns back into a string. You'll see the workaround below. To make it a bit easier for the user, we also added a current total right below so they can see, in real time, what their total is. Now let's look at the JavaScript.

``` js
const app = new Vue({
  el: '#app',
  data:{
    errors: [],
    weapons: 0,
    shields: 0,
    coffee: 0,
    ac: 0,
    mousedroids: 0
  },
  computed: {
     total: function () {
       // must parse because Vue turns empty value to string
       return Number(this.weapons) +
         Number(this.shields) +
         Number(this.coffee) +
         Number(this.ac+this.mousedroids);
     }
  },
  methods:{
    checkForm: function (e) {
      this.errors = [];

      if (this.total != 100) {
        this.errors.push('Total must be 100!');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    }
  }
})
```

We set up the total value as a computed value, and outside of that bug I ran into, it was simple enough to setup. My checkForm method now just needs to see if the total is 100 and that's it. You can play with this here:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqGoy" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 3" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqGoy/">form validation 3</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Server-side Validation

In my final example, we built something that makes use of Ajax to validate at the server. The form will ask you to name a new product and will then check to ensure that the name is unique. We wrote a quick [OpenWhisk](http://openwhisk.apache.org/) serverless action to do the validation. While it isn't terribly important, here is the logic:

``` js
function main(args) {
    return new Promise((resolve, reject) => {
        // bad product names: vista, empire, mbp
        const badNames = ['vista', 'empire', 'mbp'];

        if (badNames.includes(args.name)) {
          reject({error: 'Existing product'});
        }

        resolve({status: 'ok'});
    });
}
```

Basically any name but "vista", "empire", and "mbp" are acceptable. Ok, so let's look at the form.

``` html
<form
  id="app"
  @submit="checkForm"
  method="post"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">New Product Name: </label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

There isn't anything special here. So let's go on to the JavaScript.

``` js
const apiUrl = 'https://openwhisk.ng.bluemix.net/api/v1/web/rcamden%40us.ibm.com_My%20Space/safeToDelete/productName.json?name=';

const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: ''
  },
  methods:{
    checkForm: function (e) {
      e.preventDefault();

      this.errors = [];

      if (this.name === '') {
        this.errors.push('Product name is required.');
      } else {
        fetch(apiUrl + encodeURIComponent(this.name))
        .then(res => res.json())
        .then(res => {
          if (res.error) {
            this.errors.push(res.error);
          } else {
            // redirect to a new URL, or do something on success
            alert('ok!');
          }
        });
      }
    }
  }
})
```

We start off with a variable representing the URL of the API that is running on OpenWhisk. Now look at `checkForm`. In this version, we always prevent the form from submitting (which, by the way, could be done in the HTML with Vue as well). You can see a basic check on `this.name` being empty, and then we hit the API. If it's bad, we add an error as before. If it's good, right now we do nothing (just an alert), but you could navigate the user to a new page with the product name in the URL, or do other actions as well. You can run this demo below:

<p data-height="265" data-theme-id="0" data-slug-hash="BmgzeM" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 4" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/BmgzeM/">form validation 4</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Alternative Patterns

While this cookbook entry focused on doing form validation "by hand", there are, of course, some great Vue libraries that will handle a lot of this for you. Switching to a prepackage library may impact the final size of your application, but the benefits could be tremendous. You have code that is (most likely) heavily tested and also updated on a regular basis. Some examples of form validation libraries for Vue include:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](http://vee-validate.logaretm.com/)
