<script>
  import Card from './Card.svelte';
  import { quintOut } from 'svelte/easing';
  import { crossfade } from 'svelte/transition';
  import { flip } from 'svelte/animate';

  const [send, receive] = crossfade({
    duration: d => Math.sqrt(d * 200),
    fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
  });

  let columns = [
    {
      id: 0,
      name: 'Todo',
      cards:  [
        { id: 0, content: 'Go shopping' },
        { id: 1, content: 'Do the laundry' }
      ]
    },
    {
      id: 1,
      name: 'In progress',
      cards: [
        { id: 2, content: 'Program a kanban app' }
      ]
    },
    {
      id: 2,
      name: 'Done',
      cards: []
    }
  ];

  function createCard(columnIndex) {
    columns[columnIndex].cards = [
      ...columns[columnIndex].cards,
      {
        id: Math.random().toString(36).substring(2, 15),
        content: ''
      }
    ]
  }

  function addColumn() {
    columns = [...columns, { id: columns.length, name: 'Placeholder', cards: [] }];
  }

  function removeColumn() {
    if(columns.length > 1) {
      columns = columns.slice(0, -1);
    }
  }

  function moveToNext(columnIndex, card) {
    columns[columnIndex].cards = columns[columnIndex].cards.filter(c => c.id !== card.id);
    if(columnIndex < columns.length - 1) {
      columns[columnIndex + 1].cards = [...columns[columnIndex+1].cards, card];
    }
  }
</script>

<style>
  .app {
    height: 100vh;
    display: flex;
    flex-direction: column;
  }

  header {
    padding: 20px;
    line-height: 1;
  }

  header h1 {
    display: inline;
    font-size: 1em;
    vertical-align: middle;
  }

  .board {
    flex: 1;
    display: flex;
    padding: 20px;
    padding-top: 0;
    overflow-x: auto;
  }

  .column {
    display: flex;
    flex-direction: column;
    background-color: hsl(0, 0%, 90%);
    padding: 20px 0;
    min-width: 300px;
    width: 300px;
    margin-right: 5px;
    border-radius: 5px;
  }

  .col-title {
    display: flex;
    margin: 0 20px 1em;
  }

  .col-title .name {
    font-weight: bold;
    flex: 1;
  }

  .col-title .add {
    cursor: pointer;
    user-select: none;
  }

  .cards {
    flex: 1;
    overflow-y: auto;
    padding: 0 20px;
  }
</style>

<div class="app">
  <header>
    <h1>Kanban</h1>
    <button on:click={addColumn}>Add Column</button>
    <button on:click={removeColumn}>Remove Column</button>
  </header>
  <div class="board">
    {#each columns as column, columnIndex }
      <div class="column" in:receive="{{key: columnIndex}}" out:send="{{key: columnIndex}}">
        <div class="col-title">
          <div class="name">
            <span contenteditable bind:innerHTML={column.name}></span>
          </div>
          <div>
            <button class="add" on:click={() => createCard(columnIndex)}>Add</button>
          </div>
        </div>
        <div class="cards">
          {#each column.cards as card (card.id)}
            <div animate:flip="{{duration: 200}}" in:receive="{{key: card.id}}" out:send="{{key: card.id}}">
              <Card bind:content={card.content} on:next={() => moveToNext(columnIndex, card)} />
            </div>
          {/each}
        </div>
      </div>
    {/each}
  </div>
</div>
