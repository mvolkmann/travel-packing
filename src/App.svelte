<script>
  import Category from './Category.svelte';
  import {getGuid} from './util';

  let categories = [];
  let categoryName;
  let show = 'all';

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
  input[type=radio] {
    margin-left: 10px;
  }
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
    <label>Show</label>
    <label>
      <input name="show" type="radio" value="all" bind:group={show} />
      All
    </label>
    <label>
      <input name="show" type="radio" value="packed" bind:group={show} />
      Packed
    </label>
    <label>
      <input name="show" type="radio" value="unpacked" bind:group={show} />
      Unpacked
    </label>
  </div>

  {#each categories as category}
    <Category {category} {show} on:delete={() => deleteCategory(category)} />
  {/each}
</main>
