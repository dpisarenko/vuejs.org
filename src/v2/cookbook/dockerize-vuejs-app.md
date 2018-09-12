---
title: Eine Vue.js-Anwendung dockerisieren
type: cookbook
order: 13
---

## Einfaches Beispiel

Nehmen wir an, Du hast Deine erste Vue.js-Anwendung mit der wunderbaren [Vue.js-Webpack-Vorlage](https://github.com/vuejs-templates/webpack) gebaut. Jetzt willst Du Deine Kollegen beeindrucken und zeigen, dass sie auch in einem Docker-Container läuft.

Starten wir mit dem Erstellen eines `Dockerfile` im Wurzelverzeichnis unseres Projekts:

```docker
FROM node:9.11.1-alpine

# Installiert einen einfachen HTTP-Server, um statische Inhalte
# zur Verfügung zu stellen
RUN npm install -g http-server

# Mache das 'app'-Verzeichnis zum aktuellen Arbeitsverzeichnis
WORKDIR /app

# Kopiere sowohl 'package.json', als auch 'package-lock.json' (wenn verfügbar)
COPY package*.json ./

# Installiere die Abhängigkeiten des Projekts
RUN npm install

# Kopiree Projekt-Dateien und -Verzeichnisse in das aktuelle Arbeitsverzeichis
# (d. h. die 'app'-Mappe)
COPY . .

# Erstelle die Anwendung für Produktion mit Minifikation
RUN npm run build

EXPOSE 8080
CMD [ "http-server", "dist" ]
```

Es kann redundant erscheinen, `package.json` und `package-lock.json` und alle Projekt-Dateien und -Verzeichnisse in zwei separaten Schritten zu kopieren. Es gibt aber einen [sehr guten Grund](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/) dafür (Spoiler: Damit kann man von gecachten Docker-Schichten profitieren).

Kompilieren wir jetzt das Docker-Image unserer Vue.js-Anwendung:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Lassen wir schliesslich unsere Vue.js-Anwendung in einem Docker-Container laufen:

```bash
docker run -it -p 8080:8080 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Wir sollten in der Lage sein, auf unsere Vue.js-Anwendung über `localhost:8080` zuzugreifen.

## Ein realistisches Beispiel

Im vorigen Beispiel verwendeten wir einen einfachen Kommandozeilen-[HTTP-Server](https://github.com/indexzero/http-server) für unsere Vue.js-Anwendung. Das geht in Ordnung zum schnellen Ausprobieren und _könnte_ sogar in einfachen Produktionsszenarien gut funktionieren. Schliesslich besagt die Dokumentation:

> Es ist leistungsfähig genug für Verwendung in Produktion, aber einfach
> und hackbar genug, damit man es fürs Testen, lokale Entwicklung und Lernen
> verwenden kann.

Jedoch kann es in realistischen, komplexen Produktionsszenarien weiser sein, sich auf die Schultern eines Giganten wie [NGINX](https://www.nginx.com/) or [Apache](https://httpd.apache.org/) zu stellen. Genau das werden wir als Nächstes machen: Wir werden NGINX verwenden, um unsere Vue.js-Anwendung laufen zu lassen, weil er als eine der schnellsten und kampferprobtesten Lösung ist.

Reorganisieren wir einmal unser `Dockerfile`, so dass er NGINX verwendet:

 ```docker
# build stage
FROM node:9.11.1-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:1.13.12-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

OK, sehen wir mal, was hier passiert:

* Wir haben unser ursprüngliches `Dockerfile` in mehrere Stages aufgeteilt, indem wir die Funktionalität [Multi-stage-Builds](https://docs.docker.com/develop/develop-images/multistage-build/) von Docker verwendeten.
* Die erste Umgebung (stage) ist für das Kompilieren eines Produktions-Artefakts unserer Vue.js-Anwendung verantwortlich.
* Die zweite Umgebung (stage) ist für das Laufenlassen eines solchen Artefakts auf NGINX verantwortlich.

Jetzt werden wir das Docker-Image unserer Vue.js-Anwendung kompilieren:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Schließlich, lassen wir mal unsere Vue.js-Anwendung in einem Docker-Container laufen:

```bash
docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Wir sollten in der Lage sein, auf unsere Vue.js-Anwendung über `localhost:8080` zuzugreifen.

## Zusätzlicher Kontext

Wenn Du dieses Kochbuch liest, weisst Du wahrscheinlich, warum Du Deine Vue.js-Anwendung dockerisieren willst. Aber wenn Du auf dieser Seite gelandet bist, nachdem Du in Google die `Auf gut Glück!`-Taste gedrückt hast, werde ich Dir ein paar gute Gründe dafür geben.

Heute tendiert die Webentwicklung zum Ansatz von [Cloud-Native](https://pivotal.io/cloud-native), welches sich um die folgenden Buzzwords dreht:

* Microservices
* DevOps
* Continuous Delivery

Lass uns sehen, wie diese Konzepte die Entscheidung für und wider das Dockerisieren unserer Vue.js-Anwendung beeinflussen.

### Auswirkungen der Microservices

Wenn wir uns für die [Mikroservice-Architektur](https://martinfowler.com/microservices/) entscheiden, dann bauen wir eine Anwendung als ein System aus mehreren kleinen Diensten. Jeder davon läuft in seinem eigenen Prozess und kommuniziert mit anderen leichtgewichtigen Mechanismen. Diese Dienste werden rund um die Geschäftslogik-Funktionalitäten gebaut und unabhängig voneinander durch vollautomatische Maschinerie installiert.

Wenn wir uns verpflichten, diesem architektonischen Ansatz zu folgen, bedeutet dies, dass wir unser Frontend wie einen unabhängigen Dienst entwickeln und installieren.

### Auswirkungen von DevOps

Wenn wir die [DevOps](https://martinfowler.com/bliki/DevOpsCulture.html)-Kultur, Werkzeuge und die agilen Ingenieurspraktiken wählen, führt das, unter anderem, zu einer Netten Nebenwirkung -- die Zusammenarbeit zwischen den Entwicklern (development) und Administratoren (operations) steigt. Eines der Probleme in der Vergangenheit (und der Gegenwart in manchen Firmen) ist das Folgende. Das Entwicklungsteam interessiert sich nicht für Belange des Betriebs und der Instandhaltung des Systems sobald es an die Admins übergeben wird. Die Admins interessieren sich nicht für die Geschäftsziele des Systems und reagieren zurückhaltend, wenn es darum geht, die operativen Bedürfnisse des Systems zufriedenzustellen (auch bekannt als "die Launen der Entwickler").

Das Auslifern unserer Vue.js-Anwendung als ein Docker-Image minimiert den Unterschied zwischen dem Laufenlassen eines Dienstes auf dem Laptop eines Entwicklers, der Produktions- oder jeder anderen möglichen Umgebung.

### Effects of Continuous Delivery

By leveraging the [Continuous Delivery](https://martinfowler.com/bliki/ContinuousDelivery.html) discipline we build our software in a way that it can potentially be released to production at any time. Such engineering practice is enabled by means of what is normally called [continuous delivery pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html). The purpose of a continuous delivery pipeline is to split our build into stages (e.g. compilation, unit tests, integration tests, performance tests, etc.) and let each stage verify our build artifact whenever our software changes. Ultimately, each stage increases our confidence in the production readiness of our build artifact and, therefore, reduces the risk of breaking things in production (or any other environment for that matters).

So, creating a Docker image for our Vue.js app is a good choice here because that would represent our final build artifact, the same artifact that would be verified against our continuous delivery pipeline and that could potentially be released to production with confidence.

## Alternative Patterns

If your company is not into Docker and Kubernetes just yet or you simply want to get your MVP out the door, maybe dockerizing your Vue.js app is not what you need.

Common alternatives are:
* leveraging an all-in-one platform like [netlify](https://www.netlify.com/);
* hosting your SPA on [Amazon S3](https://aws.amazon.com/s3/) and serving it with [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (see [this](https://serverless-stack.com/chapters/deploy-the-frontend.html) link for a detailed guide).