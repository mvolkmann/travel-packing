<script>
  import Category from './Category.svelte';
  import {getGuid} from './util';

  let categories = [];
  let categoryName;
  let showPacked = true;

  createDummyData();

  function createCategory(name) {
    return {id: getGuid(), name, items: []};
  }

  function createDummyData() {
    let backpack = createCategory('Backpack');
    backpack.items.push(createItem('pens', true));
    backpack.items.push(createItem('wallet'));

    let clothes = createCategory('Clothes');
    clothes.items.push(createItem('socks', true));
    clothes.items.push(createItem('shoes'));

    categories = [backpack, clothes];
  }

  function createItem(name, packed = false) {
    return {id: getGuid(), name, packed};
  }

  function addCategory() {
    categories.push({id: getGuid(), name: categoryName, items: []});
    categories.sort((c1, c2) => c1.name.localeCompare(c2.name));
    categories = categories;
    categoryName = '';
  }

  function deleteCategory(category) {
    //TODO: Warn if contains items.
    categories = categories.filter(cat => cat.id !== category.id);
  }
</script>

<style>

</style>

<main>
  <h1>Travel Packing Checklist</h1>

  <form on:submit|preventDefault={addCategory}>
    <label>
      New Category
      <input bind:value={categoryName} />
    </label>
    <button disabled={!categoryName}>Add Category</button>
  </form>
  <p>
    Suggested categories include Backpack, Clothes, Last Minute, Medicines,
    Running Gear, and Toiletries.
  </p>

  <div>
    <label>
      <input type="checkbox" bind:checked={showPacked} />
      Show Packed Items
      <label />
    </label>
  </div>

  {#each categories as category}
    <Category
      {category}
      {showPacked}
      on:delete={() => deleteCategory(category)} />
  {/each}
</main>
