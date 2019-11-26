<script>
  import {createEventDispatcher, onMount} from 'svelte';
  const dispatch = createEventDispatcher();

  import Category from './Category.svelte';
  import {getGuid} from './util';

  let categories = [];
  let categoryName;
  let show = 'all';

  onMount(() => restore());

  $: persist(categories);

  createDummyData();

  function addCategory() {
    categories.push({id: getGuid(), name: categoryName, items: []});
    categories.sort((c1, c2) => c1.name.localeCompare(c2.name));
    categories = categories;
    categoryName = '';
  }

  function clearAllChecks() {
    for (const category of categories) {
      for (const item of category.items) {
        item.packed = false;
      }
    }
    categories = categories;
  }

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

  function deleteCategory(category) {
    //TODO: Warn if contains items.
    categories = categories.filter(cat => cat.id !== category.id);
  }

  function persist(categories) {
    console.log('Checklist.svelte persist: categories =', categories);
    localStorage.setItem('travel-packing', JSON.stringify(categories));
  }

  function restore() {
    categories = JSON.parse(localStorage.getItem('travel-packing'));
  }
</script>

<style>
  .clear {
    margin-left: 30px;
  }

  input[type='radio'] {
    margin-left: 10px;
  }

  .logout-btn {
    position: absolute;
    right: 20px;
    top: 20px;
  }

  main {
    font-size: 24px;
    margin-right: 1em;
    margin-top: 1em;
  }

  .radios {
    display: flex;
    align-items: center;
  }

  .radios > label:not(:first-of-type) {
    display: inline-flex;
    align-items: center;

    margin-left: 1em;
  }

  .radios > label > input {
    margin: 0 10px 3px 0;
  }
</style>

<main>
  <form on:submit|preventDefault={addCategory}>
    <label>
      New Category
      <input bind:value={categoryName} />
    </label>
    <button disabled={!categoryName}>Add Category</button>
    <button class="logout-btn" on:click={() => dispatch('logout')}>
      Log Out
    </button>
  </form>
  <p>
    Suggested categories include Backpack, Clothes, Last Minute, Medicines,
    Running Gear, and Toiletries.
  </p>

  <div class="radios">
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

    <button class="clear" on:click={clearAllChecks}>Clear All Checks</button>
  </div>

  {#each categories as category}
    <Category {category} {show} on:delete={() => deleteCategory(category)} />
  {/each}
</main>
