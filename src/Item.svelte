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
  }

  label {
    display: inline-block;
  }
</style>

<li>
  <label>
    <input type="checkbox" bind:checked={item.packed} />
    {item.name}
  </label>
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</li>
