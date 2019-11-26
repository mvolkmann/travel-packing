<script>
  import {createEventDispatcher} from 'svelte';
	//import {flip} from 'svelte/animate';

  export let item;

  let editing = false;

  const dispatch = createEventDispatcher();

  function handleKey(event) {
    if (event.code === 'Enter') event.target.blur();
  }
</script>

<style>
  button {
    background-color: transparent;
    border: none;
  }

  input[type='checkbox'] {
    margin-right: 8px;
  }

  label {
    display: inline-block;
  }

  .packed-true {
    color: gray;
    text-decoration: line-through;
  }
</style>

<li>
  <label class="packed-{item.packed}">
    <input type="checkbox" bind:checked={item.packed} />
    {#if editing}
      <input
        bind:value={item.name}
        on:blur={() => (editing = false)}
        on:keypress={handleKey} />
    {:else}
      <span on:click={() => (editing = true)}>{item.name}</span>
    {/if}
  </label>
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</li>
