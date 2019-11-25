<script>
  import {createEventDispatcher} from 'svelte';
  import Item from './Item.svelte';
  import {getGuid} from './util';

  export let category;

  const dispatch = createEventDispatcher();

  let itemName = '';

  //TODO: Need a way to delete categories and warn if it contains items.

  function addItem() {
    const {items} = category;
    items.push({id: getGuid(), name: itemName, packed: false});
    items.sort((item1, item2) => item1.name.localeCompare(item2.name));
    category.items = items;
  }

  function deleteItem(item) {
    category.items = category.items.filter(it => it.id !== item.id);
  }
</script>

<style>
  ul {
    list-style: none;
    margin-left: 0;
    padding-left: 0;
  }
</style>

<h3>
  {category.name}
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</h3>

<div>
  <label>
    New Item
    <input bind:value={itemName} />
  </label>
  <button disabled={!itemName} on:click={addItem}>Add Item</button>
</div>

<ul>
  {#each category.items as item}
    <Item {item} on:delete={() => deleteItem(item)} />
  {:else}
    <div>This category does not contain any items yet.</div>
  {/each}
</ul>
