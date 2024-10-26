# Backstage

https://backstage.io/
https://github.com/backstage/backstage#getting-started


# Tutorial

1. Install Backstage-App
Start die Erstellung eines neues Backstage-Entwicklerportals. Nach Aufruf des nachfolgenden Kommandos wird man nach dem Namen des neuen Portals gefragt.

```bash
npx @backstage/create-app@latest
```


2. Initialisiere die neue Applikation
Wir wechseln dann in das neue Verzeichnis und führen die erste Initialisierung durch.

```bash
cd [NAME]
yarn install
```

3. Git Repo erstellung und ersten Commit erzeugen
In dem aktuellen Verzeichnis legen wir ein Git-Repository an und sichern dies mit einem ersten Commit.

```bash
git init
git add .
git commit -m "first commit"
```

4. Backstage benötigt eine Datenbank. Die Basis Installation greift hier auf eine SQL-Lite Umgebung zurück, ich will hier aber eine parallel laufende Postgres-Umgebung einrichten, welche unter docker läuft. Dazu lege ich mir eine docker-compose.yaml im Hauptverzeichnis mit folgendem Inhalt an.

```docker-compose.yaml
services:
  db:
    image: postgres:16.4
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: backstage
      PGDATA: /data/postgres
    volumes:
      - db:/data/postgres
    ports:
      - "5432:5432"
    healthcheck:
      test: [ "CMD-SHELL", "pg_isready -d postgres" ]
      interval: 30s
      timeout: 10s
      retries: 5
  pgadmin:
    image: dpage/pgadmin4
    restart: always
    ports:
      - "8888:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@saaro.net
      PGADMIN_DEFAULT_PASSWORD: admin
    volumes:
      - pgadmin-data:/var/lib/pgadmin 
volumes:
  db:
  pgadmin-data:
```

5. Starten der Datenbank-Umgebung

```bash
docker compose up -d
```

Danach kann man die Datenbank über die mit installierte pgadmin-App beobachten. Hierzu einfach über die URL: http://localhost:8888 die App starten und mit den o.g. Email und Passwort anmelden. Nach dem ersten Aufruf muss man dann noch eine Connection zur DB selbst anlegen. Die Daten finden sich auch in der docker-compose.yaml, der Hostnamen ist "db", die Datenbank "backstage" und das Passwort "postgrest". 
Damit sollte der Zugriff auf die Datenbank gelingen.

6. Client in Backstage einrichten

```bash
yarn --cwd packages/backend add pg
```

und in der app-config.production.yaml den entsprechenden Eintrag für die lokale DB anlegen:

```bash
yarn --cwd packages/backend add pg
```

