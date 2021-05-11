---
title: 'Addons'
tocTitle: 'Addons'
description: 'Scopri come integrare ed usare il popolare Controls addon'
commit: 'a23f4d0'
---

Storybook ha un robusto ecosistema di [addons](https://storybook.js.org/docs/react/configure/storybook-addons) che puoi usare per migliorare l'esperienza di sviluppo per tutti nel tuo team. Guardati tutti [qui](https://storybook.js.org/addons),

Se hai già seguito questo tutorial avrai già incontrato diversi addon e inizializzato uno di questi nel capitolo [Testing](/intro-to-storybook/react/it/test/).

Ci sono addon per ogni possibile caso d'uso. Ci vorrebbe un'eternità per scrivere di tutti loro. Andiamo ad integrare uno dei più popolari addon: [Controls](https://storybook.js.org/docs/react/essentials/controls).

## Co cos'è Controls?

*Controls* permette a designer e developer allows designers and developers di esplorare facilmente il comportamento dei componenti _giocando_ con i suoi argomenti. Nessun codice è richiesto. *Controls* crea un pannello aggiuntivo accanto alle tue storie, in questo modo tu puoi modificare i loro argomenti in tempo reale.

Le nuove installazioni di Storybook includono *Controls* direttamente. Nessuna configurazione aggiuntiva è richiesta.

<video autoPlay muted playsInline loop>
  <source
    src="/intro-to-storybook/controls-in-action.mp4"
    type="video/mp4"
  />
</video>

## Gli Addon sbloccano nuovi flussi di lavoro di Storybook

Storybook è uno splendido [component-driven development environment](https://www.componentdriven.org/). L'addon *Controls* evolve Storybook in uno strumento di documentazione intereattivo.

### Utilizzo di Controls per trovare casi limite

Con *Controls* i QA Engineers, gli UI Engineers, o altri stakeholder possono spingere il componente al limite! Andiamo a considerare il seguente esempio, cosa succederebbe al nostro componente `Task` se aggiungessimo una stringa **MASSIVE**?

[TO COMPLETE]

![Oh no! The far right content is cut-off!](/intro-to-storybook/task-edge-case.png)

That's not right! It looks like the text overflows beyond the bounds of the Task component.

Controls allowed us to quickly verify different inputs to a component. In this case a long string. This reduces the work required to discover UI problems.

Now let's fix the issue with overflowing by adding a style to `Task.js`:

```diff:title=src/components/Task.js
<input
  type="text"
  value={title}
  readOnly={true}
  placeholder="Input title"
+ style={{ textOverflow: 'ellipsis' }}
/>
```

![That's better.](/intro-to-storybook/edge-case-solved-with-controls.png)

Problem solved! The text is now truncated when it reaches the boundary of the Task area using a handsome ellipsis.

### Adding a new story to avoid regressions

In the future, We can manually reproduce this problem by entering the same string via Controls. But it's easier to write a story that showcases this edge case. That expands our regression test coverage and clearly outlines the limits of the component(s) for the rest of the team.

Add a new story for the long text case in `Task.stories.js`:

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

Now we can reproduce and work on this edge case with ease.

<video autoPlay muted playsInline loop>
  <source
    src="/intro-to-storybook/task-stories-long-title.mp4"
    type="video/mp4"
  />
</video>

If we are [visual testing](/intro-to-storybook/react/en/test/), we'll also be informed if the ellipsizing solution breaks. Obscure edge-cases are liable to be forgotten without test coverage!

<div class="aside"><p>💡 Controls is a great way to get non-developers playing with your components and stories, and much more than we've seen here, we recommend reading the <a href="https://storybook.js.org/docs/react/essentials/controls">official documentation</a> to learn more about it. However, there are many more ways you can customize Storybook to fit your workflow with addons. In the <a href="/create-an-addon/react/en/introduction/">create an addon guide</a> we'll teach you that, by creating an addon that will help you supercharge your development workflow.</p></div>

### Merge Changes

Don't forget to merge your changes with git!
