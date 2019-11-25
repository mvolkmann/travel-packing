<script>
  import Item from './Item.svelte';
  export let category;

  function deleteItem(item) {
    category.items = category.items.filter(i => i.id !== item.id);
  }
</script>

<style>
  ul {
    list-style: none;
    margin-left: 0;
    padding-left: 0;
  }
</style>

<h3>{category.name}</h3>
<ul>
  {#each category.items as item}
    <Item item={item} on:delete={() => deleteItem(item)}/>
  {:else}
    <div>This category does not contain any items yet.</div>
  {/each}
</ul>