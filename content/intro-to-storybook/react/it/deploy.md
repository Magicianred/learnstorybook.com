---
title: 'Deploy Storybook'
tocTitle: 'Deploy'
description: 'Impara come deployare Storibook online'
commit: '8652d73'
---

Attraverso questo tutorial, abbiamo costruito componenti nell' ambiente locale della nostra macchina. Ad un certo punto, avremo bisogno di condividere il nostro lavoro per avere dei feedback dal nostro team. Procediamo quindi con il deploy di Storybook online per aiutare i nostri colleghi a rivedere l'implementazione della nostra UI.

## Esportare come app statica

Per effettuare il deploy di Storybook per prima cosa dobbiamo esportarlo come una applicazione web statica. Questa funzionalità è già integrata in Storybook e pre-configurata.

Eseguendo `yarn build-storybook` verrà generato uno Storybook statico nella cartella `storybook-static`, che potrà essere distribuito tramite un qualsiasi servizio di hosting per siti web statici.

## Pubblicare Storybook

Questo tutorial utilizza <a href="https://www.chromatic.com/">Chromatic</a>, un servizio di pubblicazione gratuito creato dai maintainers di Storybook. Chromatic ci consente di effettuare il deploy ed ospitare il nostro Storybook in modo sicuro nel cloud.

### Creazione di un repository in GitHub

Prima di iniziare, il nostro codice in locale deve essere sincronizzato con un servizio di versioning remoto. Una volta che il nostro progetto è stato inizializzato nel capitolo [Per iniziare](/intro-to-storybook/react/it/get-started/), abbiamo già inizializzato un repository locale. A questo punto abbiamo già dei commit di cui possiamo eseguire il push su di un repository remoto.

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

### Installazione di Chromatic

Aggiungete il pacchetto come dipendenza di sviluppo.

```bash
yarn add -D chromatic
```

Una volta che il pacchetto è installato, [effettuate il login a Chromatic](https://www.chromatic.com/start) con il vostro account GitHub (Chromatic richiederà solamente permessi will only ask for lightweight permissions). Then we'll create a new project called name "taskbox" and sync it with the GithHub repository we've setup.

Click `Choose GitHub repo` under collaborators and select your repo.

<video autoPlay muted playsInline loop style="width:520px; margin: 0 auto;">
  <source
    src="/intro-to-storybook/chromatic-setup-learnstorybook.mp4"
    type="video/mp4"
  />
</video>

Copy the unique `project-token` that was generated for your project. Then execute it, by issuing the following in the command line, to build and deploy our Storybook. Make sure to replace `project-token` with your project token.

```bash
yarn chromatic --project-token=<project-token>
```

![Chromatic running](/intro-to-storybook/chromatic-manual-storybook-console-log.png)

When finished, you'll get a link `https://random-uuid.chromatic.com` to your published Storybook. Share the link with your team to get feedback.

![Storybook deployed with chromatic package](/intro-to-storybook/chromatic-manual-storybook-deploy-6-0.png)

Hooray! We published Storybook with one command, but manually running a command every time we want to get feedback on UI implementation is repetitive. Ideally, we'd publish the latest version of components whenever we push code. We'll need to continuously deploy Storybook.

## Continuous deployment with Chromatic

Now that our project is hosted in a GitHub repository, we can use a continuous integration(CI) service to deploy our Storybook automatically. [GitHub Actions](https://github.com/features/actions) is a free CI service that's built into GitHub that makes automatic publishing easy.

### Add a GitHub Action to deploy Storybook

In the root folder of our project, create a new directory called `.github` then create another `workflows` directory inside of it.

Create a new file called `chromatic.yml` like the one below. Make sure to replace `project-token` with your project token.

```yaml:title=.github/workflows/chromatic.yml
# Workflow name
name: 'Chromatic Deployment'

# Event for the workflow
on: push

# List of jobs
jobs:
  test:
    # Operating System
    runs-on: ubuntu-latest
    # Job steps
    steps:
      - uses: actions/checkout@v1
      - run: yarn
        #👇 Adds Chromatic as a step in the workflow
      - uses: chromaui/action@v1
        # Options required for Chromatic's GitHub Action
        with:
          #👇 Chromatic projectToken, see https://storybook.js.org/tutorials/intro-to-storybook/react/en/deploy/ to obtain it
          projectToken: project-token
          token: ${{ secrets.GITHUB_TOKEN }}
```

<div class="aside"><p>💡 For brevity purposes <a href="https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets">GitHub secrets</a> weren't mentioned. Secrets are secure environment variables provided by GitHub so that you don't need to hard code the <code>project-token</code>.</p></div>

### Commit the action

In the command line, issue the following command to add the changes that were done:

```bash
git add .
```

Then commit them by issuing:

```bash
git commit -m "GitHub action setup"
```

Finally push them to the remote repository with:

```bash
git push origin main
```

Once you’ve set up the GitHub action. Your Storybook will be deployed to Chromatic whenever you push code. You can find all the published Storybook’s on your project’s build screen in Chromatic.

![Chromatic user dashboard](/intro-to-storybook/chromatic-user-dashboard.png)

Click the latest build, it should be the one at the top.

Then, click the `View Storybook` button to see the latest version of your Storybook.

![Storybook link on Chromatic](/intro-to-storybook/chromatic-build-storybook-link.png)

Use the link and share it with your team members. This is helpful as a part of the standard app development process or simply to show off work 💅.
