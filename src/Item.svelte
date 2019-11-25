<script>
  import {createEventDispatcher} from 'svelte';

  export let item;

  const dispatch = createEventDispatcher();
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
    {item.name}
  </label>
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</li>
