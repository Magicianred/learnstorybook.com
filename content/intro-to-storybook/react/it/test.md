---
title: 'Testare i componenti UI'
tocTitle: 'Testing'
description: 'Impara a testare i componenti UI'
---

Un tutorial Storybook non sarebbe completo senza una sezione relativa ai test. Testare i componenti è essenziale per creare UI di alta qualità. Nei sistemi modulari i piccoli ritocchi possono portare a importanti regressioni. Fin qui abbiamo incontrato tre tipi di test:

- **Test manuali** affidarsi agli sviluppatori per verificare manualmente la correttezza dei componenti. Questi test ci aiutano a verificare la corretta visualizzazione dei componenti man mano che li costruiamo.
- **Snapshot test** catturare con *Storyshots* il markup renderizzato di un componente. Questi test ci aiutano a restare al passo con i cambiamenti del markup che causano il rendering di avvertimenti ed errori.
- **Unit test** con Jest verificare che l'output di un componente rimanga uguale dato un input fisso. Sono ottimi per testare le qualità funzionali di un componente.

## “Ma è corretto?”

Sfortunatamente, i soli metodi di testing sopra citati non sono sufficienti a prevenire bug nell'interfaccia utente. Le interfacce utente sono complesse da testare poiché il design è soggettivo e ha diverse sfumature. I test manuali sono, beh, manuali. Gli *Snapshot test* generano troppi falsi positivi quando vengono utilizzati per le interfacce utente. I test unitari a livello di pixel non hanno molto valore. Una completa strategia di test tramite Storybook include inoltre test di regressione visivi.

---

## Visual testing for Storybook

I test di regressione visivi, chiamati anche test visivi, sono pensati per catturare cambiamenti nell'aspetto delle storie. Lavorano catturando degli screenshots di ciascuna storia e li comparano di commit in commit con i cambiamenti più recenti. Questa metodologia è perfetta per verificare elementi grafici come layout, colori, dimensioni e contrasti.

<video autoPlay muted playsInline loop style="width:480px; margin: 0 auto;">
  <source
    src="/intro-to-storybook/visual-regression-testing.mp4"
    type="video/mp4"
  />
</video>

Storybook è uno strumento fantastico per i test di regressione visivi poiché ogni storia è essenzialmente una specifica di un test. Ogni volta che scriviamo o aggiorniamo una storia, abbiamo una specifica gratis!

Ci sono molti strumenti per i test di regressione visivi. Noi raccomandiamo [**Chromatic**](https://www.chromatic.com/), un servizio di pubblicazione gratuito creato dai maintainers di Storybook che esegue in parallelo i test visivi nel cloud. Ci consente, inoltre, di pubblicare Storybook online come abbiamo visto nel [precedente capitolo](/intro-to-storybook/react/it/deploy/).

## Catturare una modifica della UI

I test di regressione visivi si affidano alla comparazione delle immagini del nuovo codice renderizzato con le immagini di base. Se un cambiamento nella UI viene catturato ne saremo notificati.

Vediamo come funziona modificando lo sfondo del componente `Task`.

Iniziamo creando un nuovo branch per questa modifica:

```bash
git checkout -b change-task-background
```

Modifichiamo `src/components/Task.js` come segue:

```diff:title=src/components/Task.js
<div className="title">
  <input
    type="text"
    value={title}
    readOnly={true}
    placeholder="Input title"
+   style={{ background: 'red' }}
  />
</div>
```

Questo produce un nuovo colore di sfondo per l'elemento.

![task background change](/intro-to-storybook/chromatic-task-change.png)

Aggiungiamo il file:

```bash
git add .
```

Effettuiamo il commit:

```bash
git commit -m "change task background to red"
```

ed effettuaimo il push delle modifiche al repository remoto:

```bash
git push -u origin change-task-background
```

Infine, apriamo il repository GitHub ed apriamo una pull request per il branch `change-task-background`.

![Creating a PR in GitHub for task](/github/pull-request-background.png)

Aggiungiamo un testo descrittivo alla nostra pull request e clicchiamo `Create pull request`. Clicchiamo il check della pull request "🟡 UI Tests" in fondo alla pagina.

![Created a PR in GitHub for task](/github/pull-request-background-ok.png)

Questo ci mostrerà i cambiamenti della UI changes catturati dal nostro commit.

![Chromatic caught changes](/intro-to-storybook/chromatic-catch-changes.png)

Ci sono moltissimi cambiamenti! La gerarchia dei componenti dove `Task` è un figlio di `TaskList` e `Inbox` significa che un piccolo cambiamento si riflette in grosse regressioni. Questa circostanza è il motivo esatto per cui gli sviluppatori necessitano dei test di regressione visivi in aggiunta alle altre metodologie di test.

![UI minor tweaks major regressions](/intro-to-storybook/minor-major-regressions.gif)

## Revisione dei cambiamenti

I test di regressione visivi assicurano che i componenti non vengano modificati accidentalmente. Ma è comunque una nostra decisione quella di determinare quali cambiamenti sono intenzionali o meno.

Se una modifica è intenzionale avremo bisogno di aggiornare le immagini di base in modo che i test futuri saranno confrontati con l'ultima versione della storia. Se un cambiamento non è intenzionale dovrà essere sistemato.

<video autoPlay muted playsInline loop style="width:480px; margin: 0 auto;">
  <source
    src="/intro-to-storybook/website-workflow-review-merge-optimized.mp4"
    type="video/mp4"
  />
</video>

Dal momento che le applicazioni moderne sono costruite tramite componenti, è importante che il test venga eseguito a livello di componente. Facendo questo aiutiamo a focalizzare la causa principale del cambiamento, il componente, invece che agire sui sintomi di un cambiamento, ovvero le schermate ed i componenti compositi.

## Unire le modifiche

Quando abbiamo terminato la nostra revisione siamo pronti per unire le modifiche della UI in sicurezza --sapendo che gli aggiornamenti non introdurranno bug accidentalmente. Se vi piace il nuovo sfondo `red` accettate le modifiche altrimenti ripristinate lo stato precedente.

![Changes ready to be merged](/intro-to-storybook/chromatic-review-finished.png)

Storybook ci aiuta a **costruire** componenti; i test ci aiutano a **mantenerli**. I quattro tipi di test della UI trattati in questo tutorial sono manuali, snapshot, unitari, ed i test di regressione visivi. Gli ultimi tre possono essere automatizzati aggiungendoli alla CI non appena abbiamo finito di configurarli. Questo ci aiuta a consegnare componenti senza preoccuparci di bug nascosti. L'intero workflow è illustrato di seguito.

![Visual regression testing workflow](/intro-to-storybook/cdd-review-workflow.png)
