# Treino em Casa

PWA de treino em casa (PPL-UL adaptado) com animações próprias de cada exercício, registro diário e sequência de treinos.

Publicado em: https://ghastcmd.github.io/treino-em-casa/

## Estrutura

Tudo fica em `index.html`, em três camadas independentes:

| Camada | Onde | O que é |
| --- | --- | --- |
| Animações | `EX` | Poses-chave de cada exercício para o boneco (rig em SVG). Chave = id da animação. |
| Catálogo | `LIB` | Todos os exercícios já usados no app, inclusive os que saíram do plano: nome, dose padrão, dica, link do vídeo, grupo muscular e equipamento. `anim` aponta para outra animação quando duas variações usam o mesmo movimento (ex.: `chin_neg` usa `pull_neg`). |
| Plano | `DEFAULT_PLAN` | JSON puro com blocos de aquecimento e os 7 dias. Cada dia só referencia ids do `LIB`. |

### Formato do plano (versão 1)

```js
{
  version: 1,
  blocks: { MOB: { title, hint, items: ['mob_knee', ...] }, ... },
  days: [
    { key: 'seg', wd: 1, short: 'Seg', name: 'Segunda', focus: 'Push', desc: '...',
      warm: ['MOB', 'SHO'],                 // ids de blocos
      main: ['push_floorpress', {id: 'push_floorfly', cue: '...'}] }, // id ou override
    ...
  ]
}
```

Um item pode ser só o id ou `{id, name?, dose?, cue?}` para sobrescrever o catálogo naquele dia.

### Para o futuro montador de dias

- `loadPlan()` lê o plano salvo em `localStorage['treino-plano']` (cai no `DEFAULT_PLAN` se não houver ou se for inválido).
- `savePlan(plan)` valida e salva. Depois de salvar, recarregar a página aplica o novo plano.
- `LIB` + `GROUPS` já alimentam a aba **Biblioteca**, que é a base natural para escolher exercícios.

### Dados salvos no aparelho (localStorage)

| Chave | Conteúdo |
| --- | --- |
| `treino-feito-AAAA-MM-DD` | Exercícios marcados no dia: `{ "dia:bloco:id": {n: nome, d: dose} }` |
| `treino-notas-AAAA-MM-DD` | Anotações por exercício no dia |
| `treino-historico` | Dias concluídos: `{ "AAAA-MM-DD": 1 }` |
| `treino-inicio` | Primeiro dia de uso (dias anteriores não contam como falta) |
| `treino-plano` | Plano personalizado (opcional) |
