---
title: Erstellen eines CMS-getriebenen Blogs
type: cookbook
order: 5
---

Du hast also Deine Vue.js-Webseite gestartet, Gratulation! Jetzt willst Du ein Blog hinzufügen, das sich leicht in die Webseite einbauen lässt und für das Du keinen eigenen Server aufsetzen musst (wie dies bei WordPress und jeder anderen Datebankgetriebenen CMS der Fall ist). Du willst in der Lage sein, einfach einige wenige Vue.js Blogkomponenten und Routen hinzuzufügen und danach soll alles funktionieren, richtig? Wonach Du suchst ist ein Blog, das zur Gänze durch APIs getrieben wird, die Du direkt aus Deiner Vue.js-Anwendung heraus konsumieren kannst. Diese Anleitung wird Dir beibringen, wie Du genau das tun kannst. Lass uns einsteigen!

Wir werden schnell ein CMS-getriebenes Blog mit Vue.js bauen. Es verwendet [ButterCMS](https://buttercms.com/), ein CMS mit der "API hat Vorrang"-Philosophie. Es erlaubt Dir, die Inhalte über die ButterCMS-Konsole zu verwalten und die Inhalts-API in die Vue.js-Anwendung zu integrieren. Du kannst ButterCMS für neue und existierende Vue.js-Projekte verwenden.

![ButterCMS-Konsole](https://user-images.githubusercontent.com/160873/36677285-648798e4-1ad3-11e8-9454-d22fca8280b7.png "Butter Dashboard")

## Installieren

Führe den folgenden Befehl in der Kommandozeile aus:

`npm install buttercms --save`

Butter kann auch über ein CDN geladen werden:

`<script src="https://cdnjs.buttercms.com/buttercms-1.1.0.min.js"></script>`

## Schneller Start

Setze Deinen API-Token:

`var butter = require('buttercms')('Dein_API_Token');`

Mit ES6:

```javascript
import Butter from 'buttercms';
const butter = Butter('Dein_API_Token');
```

Mit CDN:

```html
<script src="https://cdnjs.buttercms.com/buttercms-1.1.0.min.js"></script>
<script>
  var butter = Butter('your_api_token');
</script>
```

Importiere diese Datei in jede Komponente, wo Du ButterCMS verwenden willst. Dann führe folgenden Befehl in der Konsole aus:

```javascript
butter.post.list({page: 1, page_size: 10}).then(function(response) {
  console.log(response)
})
```

Diese API-Anfrage holt Deine Blog-Beiträge. Dein Konto hat einen Beispiel-Beitrag, den Du in der Antwort sehen wirst.

## Beiträge anzeigen

Um Blogbeiträge anzuzeigen werden wir in unserer Anwendung eine `/blog`-Route erstellen (mittels Vue Router) und Blog-Beiträge aus der Butter API holen. Wir werden zusätzlich die Route `/blog/:slug` einführen, über die jeder Blog-Beitrag für sich abrufbar sein wird.

Siehe die [API-Referenz](https://buttercms.com/docs/api/?javascript#blog-posts) für mehr Optionen wie das Filtern nach Kategorie oder Autor. Die Antwort enthält auch Metadaten, die wir für die seitenweise Ausgabe verwenden werden.

`router/index.js:`

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import BlogHome from '@/components/BlogHome'
import BlogPost from '@/components/BlogPost'

Vue.use(Router)

export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/blog/',
      name: 'blog-home',
      component: BlogHome
    },
    {
      path: '/blog/:slug',
      name: 'blog-post',
      component: BlogPost
    }
  ]
})
```

Erstelle dann die Datei `components/BlogHome.vue` -- das wird unsere Blog-Homepage sein. Dort werden die letzten Beiträge aufgelistet werden.


```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-home',
    data() {
      return {
        page_title: 'Blog',
        posts: []
      }
    },
    methods: {
      getPosts() {
        butter.post.list({
          page: 1,
          page_size: 10
        }).then((res) => {
          this.posts = res.data.data
        })
      }
    },
    created() {
      this.getPosts()
    }
  }
</script>

<template>
  <div id="blog-home">
      <h1>{{ page_title }}</h1>
	  <!-- Erstelle `v-for` und wende `key` auf Vue an. Hier verwenden wir eine Kombination von slug und index. -->
      <div
        v-for="(post,index) in posts"
        :key="post.slug + '_' + index"
      >
        <router-link :to="'/blog/' + post.slug">
          <article class="media">
            <figure>
			  <!-- Binde die Ergebnisse mit `:` -->			  
              <!-- Verwende `v-if`/`else` wenn es ein `featured_image` gibt -->
              <img
                v-if="post.featured_image"
                :src="post.featured_image"
                alt=""
              >
              <img
                v-else
                src="http://via.placeholder.com/250x250"
                alt=""
              >
            </figure>
            <h2>{{ post.title }}</h2>
            <p>{{ post.summary }}</p>
          </article>
        </router-link>
      </div>
  </div>
</template>
```

So schaut es aus (beachte, dass wir die CSS-Stile aus https://bulma.io/ hinzugefügt haben, um Zeit zu sparen):

![buttercms-bloglist](https://user-images.githubusercontent.com/160873/36868500-1b22e374-1d5e-11e8-82a0-20c8dc312716.png)

Erstelle jetzt die Datei `components/BlogPost.vue`, in der ein einzelner Blog-Beitrag dargestellt werden wird.

```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-post',
    data() {
      return {
        post: {}
      }
    },
    methods: {
      getPost() {
        butter.post.retrieve(this.$route.params.slug)
          .then((res) => {
            this.post = res.data
          }).catch((res) => {
            console.log(res)
          })
      }
    },
    created() {
      this.getPost()
    }
  }
</script>

<template>
  <div id="blog-post">
    <h1>{{ post.data.title }}</h1>
    <h4>{{ post.data.author.first_name }} {{ post.data.author.last_name }}</h4>
    <div v-html="post.data.body"></div>

    <router-link
      v-if="post.meta.previous_post"
      :to="/blog/ + post.meta.previous_post.slug"
      class="button"
    >
      {{ post.meta.previous_post.title }}
    </router-link>
    <router-link
      v-if="post.meta.next_post"
      :to="/blog/ + post.meta.next_post.slug"
      class="button"
    >
      {{ post.meta.next_post.title }}
    </router-link>
  </div>
</template>
```

Hier is eine Vorschau:

![buttercms-blogdetail](https://user-images.githubusercontent.com/160873/36868506-218c86b6-1d5e-11e8-8691-0409d91366d6.png)

Jetzt holt sich unsere Anwendung alle Blog-Beiträge herunter und wir können sie einzeln betrachten. Jedoch funktionieren die Schaltflächen "Nächster Beitrag" und "Voriger Beitrag" nicht.

Eine Sache solltest Du beachten, wenn Du parametrisierte Routen verwendest: Wenn der Benutzer von `/blog/foo` nach `/blog/bar` navigiert, wird die selbe Komponenteninstanz wiederverwendet. Weil beide Routen die selbe Komponente rendern, ist dies effizienter als eine Instanz zu zerstören und eine neue zu kreieren.

<p class="tip">Beachte, dass die durch Verwendung der Komponente auf diese Weise die Lebenszyklushooks nicht aufgerufen werden. Besuche die Dokumentation von Vue Router, um mehr über das [dynamische Routen-Matching](https://router.vuejs.org/en/essentials/dynamic-matching.html) zu erfahren.</p>

Um dieses umzusetzen, müssen wir das Objekt `$route` beobachten und `getPost()` aufrufen, wenn sich die Route verändert.

Aktualisierter `<script>`-Abschnitt in `components/BlogPost.vue`:

```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-post',
    data() {
      return {
        post: {}
      }
    },
    methods: {
      getPost() {
        butter.post.retrieve(this.$route.params.slug)
          .then((res) => {
            // console.log(res.data)
            this.post = res.data
          }).catch((res) => {
            console.log(res)
          })
      }
    },
    watch: {
      $route(to, from) {
        this.getPost()
      }
    },
    created() {
      this.getPost()
    }
  }
</script>
```

Jetzt hat unsere Anwendung ein funktionierendes Blog, welches einfach über die ButterCMS-Konsole aktualisiert werden kann.

## Kategorien, Tags und Autoren

Verwende die API von Butter, um die Kategorien, Tags, und Autoren in den Blog-Beiträgen darzustellen und die Inhalte eines Blogs zu filtern.

Siehe die ButterCMS API-Referenz für mehr Information über diese Objekte:

* [Kategorien](https://buttercms.com/docs/api/?ruby#categories)
* [Tags](https://buttercms.com/docs/api/?ruby#tags)
* [Autoren](https://buttercms.com/docs/api/?ruby#authors)

Hier ist ein Beispiel für die Auflistung aller Kategorien und für das Filtern von Blogbeiträgen nach Kategorie. Rufe diese Methoden im Lebenszyklushook `created()` auf:

```javascript
methods: {
  ...
  getCategories() {
    butter.category.list()
      .then((res) => {
        console.log('Liste der Kategorien:')
        console.log(res.data.data)
      })
  },
  getPostsByCategory() {
    butter.category.retrieve('example-category', {
        include: 'recent_posts'
      })
      .then((res) => {
        console.log('Beiträge einer bestimmten Kategorie:')
        console.log(res)
      })
  }
},
created() {
  ...
  this.getCategories()
  this.getPostsByCategory()
}
```

## Alternative Muster

Ein alternatives Muster, das Du erwägen kannst, insbesondere, wenn Du ausschliesslich in Markdown schreiben willst, ist etwas wie [Nuxtent](https://nuxtent.now.sh/guide/writing#async-components). Nuxtent erlaubt Dir, `Vue Komponenten` innerhalb von Markdown-Dateien zu verwenden. Dieser Ansatz ist ähnlich zu statischen Webseitengeneratorn (d. h. Jekyll), wo Du die Blogbeiträge als Markdown-Dateien verfasst. Nuxtent fügt eine nette Integration zwishen Vue.js und Markdown hinzu, die es Dir erlaubt, zu 100 % in einer Vue.js-Welt zu leben.

## Fazit

Das war's! Du hast jetzt ein voll funktionales CMS-getriebenes Blog, welches in Deiner App läuft. Wir hoffen, dass dieses Tutorial hilfreich war und Deine Erfahrung mit Vue.js geniessbarer gemacht hat :)