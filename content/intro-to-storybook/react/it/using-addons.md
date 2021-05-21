---
title: 'Addons'
tocTitle: 'Addons'
description: 'Scopri come integrare ed usare il popolare Controls addon'
commit: 'a23f4d0'
---

Storybook ha un robusto ecosistema di [addons](https://storybook.js.org/docs/react/configure/storybook-addons) che puoi usare per migliorare l'esperienza di sviluppo per tutti nel tuo team. Vedili tutti [qui](https://storybook.js.org/addons),

Se hai già seguito questo tutorial avrai già incontrato diversi addon e inizializzato uno di questi nel capitolo [Testing](/intro-to-storybook/react/it/test/).

Ci sono addon per ogni possibile caso d'uso. Ci vorrebbe un'eternità per descriverli tutti. Andiamo ad integrare uno dei più popolari addon: [Controls](https://storybook.js.org/docs/react/essentials/controls).

## Che cos'è Controls?

*Controls* permette a designer e developer di esplorare facilmente il comportamento dei componenti _giocando_ con i suoi argomenti. Nessun codice è richiesto. *Controls* crea un pannello aggiuntivo accanto alle tue storie, in questo modo tu puoi modificare i loro argomenti in tempo reale.

Le nuove installazioni di Storybook includono direttamente *Controls*. Nessuna configurazione aggiuntiva è richiesta.

<video autoPlay muted playsInline loop>
  <source
    src="/intro-to-storybook/controls-in-action.mp4"
    type="video/mp4"
  />
</video>

## Gli Addon sbloccano nuovi flussi di lavoro di Storybook

Storybook è uno splendido [component-driven development environment](https://www.componentdriven.org/). L'addon *Controls* evolve Storybook in uno strumento di documentazione interattivo.

### Utilizzo di Controls per trovare casi limite

Con *Controls* i QA Engineers, gli UI Engineers, o altri stakeholder possono spingere il componente al limite! Andiamo a considerare il seguente esempio, cosa succederebbe al nostro componente `Task` se aggiungessimo una stringa **LUNGHISSIMA**?

![Oh no! Il contenuto a destra è tagliato!](/intro-to-storybook/task-edge-case.png)

Non va bene! Sembra che il testo vada oltre i limiti del componente Task.

Controls ci permette di verificare rapidamente diversi input per un componente. In questo caso una lunga stringa. Questo riduce il lavoro necessario per scoprire i problemi dell'interfaccia utente.

Andiamo a risolvere il problema di overflow aggiungendo uno stile a `Task.js`:

```diff:title=src/components/Task.js
<input
  type="text"
  value={title}
  readOnly={true}
  placeholder="Input title"
+ style={{ textOverflow: 'ellipsis' }}
/>
```

![Così va meglio.](/intro-to-storybook/edge-case-solved-with-controls.png)

Problema risolto! Il testo viene troncato quando raggiunge il bordo dell'area delle attività utilizzando dei bei puntini di sospensione.

### Aggiungere una nuova storia per evitare regressioni

In futuro possiamo riprodurre manualmente questo problema inserendo la stessa stringa tramite Controls. Però è più facile scrivere una storia che dimostri questo caso limite. Questo espande la copertura dei nostri test di regressione e delinea chiaramente i limiti dei componenti al resto del team.

Aggiungi una nuova storia per il caso del testo lungo in `Task.stories.js`:

```js:title=src/components/Task.stories.js
const longTitleString = `This task's name is absurdly large. In fact, I think if I keep going I might end up with content overflow. What will happen? The star that represents a pinned task could have text overlapping. The text could cut-off abruptly when it reaches the star. I hope not!`;

export const LongTitle = Template.bind({});
LongTitle.args = {
  task: {
    ...Default.args.task,
    title: longTitleString,
  },
};
```

Ora possiamo riprodurre e lavorare su questo caso limite con facilità.

<video autoPlay muted playsInline loop>
  <source
    src="/intro-to-storybook/task-stories-long-title.mp4"
    type="video/mp4"
  />
</video>

Se stiamo [testando visivamente](/intro-to-storybook/react/en/test/), verremo informati anche se la soluzione degli ellissi si rompe. I casi limiti non visibili possono essere dimenticati facilmente senza dei test di copertura!

<div class="aside"><p>💡 Controls è un ottimo modo per far giocare i non programmatori con i tuoi componenti e le storie, molto di più di quanto abbiamo visto qui, raccomandiamo di leggere la <a href="https://storybook.js.org/docs/react/essentials/controls">documentazione ufficiale</a> per saperne di più. Tuttavia ci sono molti altri modi per personalizzare Storybook e adattarlo al tuo flusso di lavoro con gli addon. Nella <a href="/create-an-addon/react/it/introduction/">guida per creare gli addon</a> te lo insegniamo, creando un addon che ti aiuterà a potenziare il tuo flusso di sviluppo.</p></div>

### Mergia le modifiche

Non dimenticarti di mergiare le tue modifiche con git!
