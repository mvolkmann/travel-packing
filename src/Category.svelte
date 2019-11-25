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
    itemName = '';
  }

  function deleteItem(item) {
    category.items = category.items.filter(it => it.id !== item.id);
  }
</script>

<style>
  section {
    --padding: 10px;

    border: solid red 1px;
    border-radius: var(--padding);
    display: inline-block;
    margin: var(--padding);
    padding: var(--padding);
  }

  ul {
    list-style: none;
    margin-left: 0;
    padding-left: 0;
  }
</style>

<section>
  <h3>
    {category.name}
    <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
  </h3>

  <form on:submit|preventDefault={addItem}>
    <label>
      New Item
      <input bind:value={itemName} />
    </label>
    <button disabled={!itemName}>Add Item</button>
  </form>

  <ul>
    {#each category.items as item}
      <Item {item} on:delete={() => deleteItem(item)} />
    {:else}
      <div>This category does not contain any items yet.</div>
    {/each}
  </ul>
</section>
