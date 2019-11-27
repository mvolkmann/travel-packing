<script>
  import {createEventDispatcher} from 'svelte';
  //import {flip} from 'svelte/animate';

  export let categoryId;
  export let dnd;
  export let item;

  let editing = false;

  const dispatch = createEventDispatcher();

  function handleKey(event) {
    const {code} = event;
    if (code === 'Enter' || code === 'Escape') event.target.blur();
  }
</script>

<style>
  button {
    background-color: transparent;
    border: none;
  }

  input[type='checkbox'] {
    --size: 24px;
    height: var(--size);
    width: var(--size);
  }

  input[type='text'] {
    border: solid lightgray 1px;
  }

  li {
    display: inline-flex;
    align-items: center;
    /* This is needed to counteract the "section *" setting
       of "pointer-events: none" in Category.svelte. */
    pointer-events: auto;
  }

  .packed-true {
    color: gray;
    text-decoration: line-through;
  }

  span {
    margin: 0 10px;
  }
</style>

<li
  draggable={true}
  on:dragstart={event => dnd.drag(event, categoryId, item.id)}>
  <input type="checkbox" bind:checked={item.packed} />
  {#if editing}
    <input
      autofocus
      bind:value={item.name}
      on:blur={() => (editing = false)}
      on:keydown={handleKey}
      type="text" />
  {:else}
    <span class="packed-{item.packed}" on:click={() => (editing = true)}>
      {item.name}
    </span>
  {/if}
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</li>
