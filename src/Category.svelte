<script>
  import {createEventDispatcher} from 'svelte';
  import Item from './Item.svelte';
  import {getGuid} from './util';

  export let category;
  export let showPacked;

  const dispatch = createEventDispatcher();

  let editing = false;
  let itemName = '';

  $: remaining = category.items.filter(item => !item.packed).length;
  $: status = `${remaining} of ${category.items.length} remaining`;

  function addItem() {
    const {items} = category;
    items.push({id: getGuid(), name: itemName, packed: false});
    items.sort((item1, item2) => item1.name.localeCompare(item2.name));
    category.items = items;
    itemName = '';
  }

  function deleteItem(item) {
    //TODO: Warn if it contains items.
    category.items = category.items.filter(it => it.id !== item.id);
  }
</script>

<style>
  h3 {
    display: flex;
    justify-content: space-between;
    align-items: center;

    margin: 0;
  }

  section {
    --padding: 10px;

    border: dashed gray 1px;
    border-radius: var(--padding);
    display: inline-block;
    margin: var(--padding);
    padding: calc(var(--padding) * 2);
    padding-top: var(--padding);
    vertical-align: top;
  }

  .status {
    font-size: 18px;
    font-weight: normal;
    margin: 0 15px;
  }

  ul {
    list-style: none;
    margin: 0;
    padding-left: 0;
  }
</style>

<section>
  <h3>
    {#if editing}
      <input bind:value={category.name} on:blur={() => editing = false}/>
    {:else}
      <span on:click={() => editing = true}>{category.name}</span>
    {/if}
    <span class="status">{status}</span>
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
      {#if !item.packed || showPacked}
        <!-- This bind causes the category object to update
           when the item packed value is toggled. -->
        <Item bind:item on:delete={() => deleteItem(item)} />
      {/if}
    {:else}
      <div>This category does not contain any items yet.</div>
    {/each}
  </ul>
</section>
