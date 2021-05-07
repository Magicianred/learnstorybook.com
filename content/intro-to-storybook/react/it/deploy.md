---
title: 'Deploy Storybook'
tocTitle: 'Deploy'
description: 'Impara come deployare Storibook online'
commit: '8652d73'
---

Attraverso questo tutorial, abbiamo costruito dei componenti nell'ambiente locale della nostra macchina. Ad un certo punto, avremo bisogno di condividere il nostro lavoro per avere dei feedback dal nostro team. Procediamo quindi con il deploy di Storybook online per aiutare i nostri colleghi a rivedere l'implementazione della nostra UI.

## Esportare come app statica

Per effettuare il deploy di Storybook per prima cosa dobbiamo esportarlo come una applicazione web statica. Questa funzionalità è già integrata in Storybook e pre-configurata.

Eseguendo `yarn build-storybook` verrà generato uno Storybook statico nella cartella `storybook-static`, che potrà essere distribuito tramite un qualsiasi servizio di hosting per siti web statici.

## Pubblicare Storybook

Questo tutorial utilizza <a href="https://www.chromatic.com/">Chromatic</a>, un servizio di pubblicazione gratuito creato dai maintainers di Storybook. Chromatic ci consente di effettuare il deploy ed ospitare il nostro Storybook in modo sicuro nel cloud.

### Creazione di un repository in GitHub

Prima di iniziare, il nostro codice in locale deve essere sincronizzato con un servizio di versioning remoto. Creando il nostro progetto seguendo i passi del capitolo [Per iniziare](/intro-to-storybook/react/it/get-started/), abbiamo già inizializzato un repository locale. A questo punto abbiamo già dei commit di cui possiamo eseguire il push su di un repository remoto.

Effettuate l'accesso a GitHub e create un nuovo repository per il progetto [qui](https://github.com/new). Date il nome “taskbox” al repository, come nel nostro progetto locale.

![GitHub setup](/intro-to-storybook/github-create-taskbox.png)

Prendendo l'URL di origine del nostro nuovo repository remoto, aggiungetelo al vostro progetto git con questo comando:

```bash
$ git remote add origin https://github.com/<your username>/taskbox.git
```

Infine, eseguite il push del repository locale su quello remoto di GitHub con:

```bash
$ git push -u origin main
```

### Ottenere Chromatic

Aggiungete il pacchetto come dipendenza di sviluppo.

```bash
yarn add -D chromatic
```

Una volta che il pacchetto è installato, [effettuate il login a Chromatic](https://www.chromatic.com/start) con il vostro account GitHub (Chromatic richiederà solamente pochi permessi). In seguito create un nuovo progetto chiamato "taskbox" e sincronizzatelo con il repository GitHub creato in precedenza.

Cliccate `Choose from GitHub` e selezionate il vostro repository.

<video autoPlay muted playsInline loop style="width:520px; margin: 0 auto;">
  <source
    src="/intro-to-storybook/chromatic-setup-learnstorybook.mp4"
    type="video/mp4"
  />
</video>

Copiate il `project-token` unico che è stato generato per il vostro progetto. In seguito eseguite il comando seguente nella command line, per effettuare la build ed il deploy del vostro Storybook. Assicuratevi di sostituire `project-token` con il vostro project token.

```bash
yarn chromatic --project-token=<project-token>
```

![Chromatic running](/intro-to-storybook/chromatic-manual-storybook-console-log.png)

Quando avrete completato tutti gli step, otterrete un link `https://random-uuid.chromatic.com` del vostro Storybook pubblicato. Condividete il link con il vostro team per ottenere dei feedback.

![Storybook deployed with chromatic package](/intro-to-storybook/chromatic-manual-storybook-deploy-6-0.png)

Urrà! Abbiamo pubblicato Storybook con un comando, ma eseguire un comando manualmente ogni volta che vogliamo ricevere un feedback sull'implementazione della UI è ripetitivo. Idealmente, vorremmo pubblicare l'ultima versione dei componenti ogni volta eseguiamo un push del codice. Abbiamo bisogno di integrare un sistema di deploy continui di Storybook.

## Deployment continuo con Chromatic

Ora che il nostro progetto è hostato in un repository GitHub, possiamo utilizzare il servizio di continuous integration (CI) per effettuare il deploy di Storybook automaticamente. [GitHub Actions](https://github.com/features/actions) è un servizio di CI gratuito sviluppato da GitHub per facilitare le operazioni di pubblicazione.

### Aggiungere una GitHub Action per il deploy di Storybook

Nella cartella root del vostro progetto, create una nuova cartella chiamata `.github` ed in seguito createne un'altra denominata `workflows` all'interno della precedente.

Create un nuovo file chiamato `chromatic.yml` come quello sottostante. Assicuratevi di sostituire `project-token` con il vostro project token.

```yaml:title=.github/workflows/chromatic.yml
# Workflow name
name: 'Chromatic Deployment'

# Evento per il workflow
on: push

# Lista dei jobs
jobs:
  test:
    # Sistema operativo
    runs-on: ubuntu-latest
    # Job steps
    steps:
      - uses: actions/checkout@v1
      - run: yarn
        #👇 Aggiungere Chromatic come step nel workflow
      - uses: chromaui/action@v1
        # Opzioni richieste per la GitHub Action di Chromatic
        with:
          #👇 Chromatic projectToken, vedi https://storybook.js.org/tutorials/intro-to-storybook/react/en/deploy/ per ottenerlo
          projectToken: project-token
          token: ${{ secrets.GITHUB_TOKEN }}
```

<div class="aside"><p>💡 Con lo scopo di essere sintetici, i <a href="https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets">GitHub secrets</a> non sono stati menzionati. I Secrets sono variabili d'ambiente sicure fornite da GitHub in modo da non dover ricorrere all'hard code del <code>project-token</code>.</p></div>

### Commit della action

Nella command line, eseguite il seguente comando per aggiungere le modifiche fatte:

```bash
git add .
```

In seguito effettuate il commit tramite:

```bash
git commit -m "GitHub action setup"
```

Ed infine effettuate il push del commit verso il repository remoto con:

```bash
git push origin main
```

Una volta creata la Action di GitHub, verrà eseguito il deploy del vostro Storybook su Chromatic ogni qual volta verrà effettuato un push del codice. Potete trovare tutti gli Storybook pubblicati nella sezione build del vostro progetto di Chromatic.

![Chromatic user dashboard](/intro-to-storybook/chromatic-user-dashboard.png)

Cliccate sull'ultima build, dovrebbe essere la prima in alto.

In seguito, cliccate sul pulsante `View Storybook` per visualizzare l'ultima versione del vostro Storybook.

![Storybook link on Chromatic](/intro-to-storybook/chromatic-build-storybook-link.png)

Utilizzate il link e condividetelo con i membri del vostro team. Questo sarà d'aiuto come parte del vostro processo di sviluppo standard delle app o semplicemente per mostrare il vostro lavoro 💅.
