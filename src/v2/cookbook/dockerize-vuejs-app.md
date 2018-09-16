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

### Auswirkungen der Continuous Delivery

Wenn wir die [Continuous Delivery](https://martinfowler.com/bliki/ContinuousDelivery.html)-Disziplin wirksam einsetzen, dann bauen wir unsere Software so, dass sie jederzeit in die Produktionsumgebung installiert werden kann. Eine solche Ingenieurspraktik wird meistens mittels einer [continuous delivery-Pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html) umgesetzt. Deren Zweck besteht darin, den Build-Prozess in Etappen zu unterteilen (z. B. Kompilieren, Unit-Tests ausführen, Integrationstests ausführen, Performance-Tests ausführen etc.), sodass jede dieser Etappen das fertig kompilierte Programm nach jeder Änderung überprüft. Jede Etappe erhöht unsere Zuversicht, dass die Software bereit für den Produktionseinsatz ist. Daher wird das Risiko reduziert, dass Fehler in der Produktons- oder jeder anderen Umgebung auftreten.

Das Erstellen eines Docker-Image für unsere Vue.js-Anwendung ist eine gute Wahl, weil das Docker-Image das letztendliche Build-Artefakt darstellen würde. Das gleiche Artefakt würde durch unsere continous delivery pipeline überprüft werden. Danach könnte man es in der Produktionsumgebung mit Zuversicht einsetzen.

## Alternative Muster

Wenn Deine Firma Docker und Kubernetes noch nicht verwendet, oder du ganz einfach Dein MVP so schnell wie möglich fertigstellen willst, dann ist vielleicht das Dockerisieren Deiner Anwendung nicht das Richtige für Dich.

Übliche Alternativen sind:
* Verwendung einer Alles-in-Einem-Plattform wie [netlify](https://www.netlify.com/);
* Hosten Deiner SPA auf [Amazon S3](https://aws.amazon.com/s3/) und Laufenlassen mit [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (siehe [diesen](https://serverless-stack.com/chapters/deploy-the-frontend.html) Link mit einer detaillierten Anleitung).