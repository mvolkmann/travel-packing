<script>
  import {createEventDispatcher, onMount} from 'svelte';
  import {flip} from 'svelte/animate';
  const dispatch = createEventDispatcher();

  import Category from './Category.svelte';
  import {getGuid} from './util';

  const options = {duration: 700};

  let categories;
  let categoryName;
  let show = 'all';

  const dragAndDrop = {
    hoveringOver: null,
    drag(event, categoryId, itemId) {
      const data = {categoryId, itemId};
      event.dataTransfer.setData('text/plain', JSON.stringify(data));
    },
    drop(event, categoryId) {
      const json = event.dataTransfer.getData('text/plain');
      const data = JSON.parse(json);

      // Remove the item from one category.
      const category = categories[data.categoryId];
      const item = category.items[data.itemId];
      delete category.items[data.itemId];

      // Add the item to another category.
      categories[categoryId].items[data.itemId] = item;

      // Trigger Svelte update.
      categories = categories;

      this.hoveringOver = null;
    }
  };

  onMount(restore);

  // Any time categories changes, persist it to localStorage.
  $: persist(categories);

  function addCategory() {
    const id = getGuid();
    categories[id] = {id, name: categoryName, items: []};
    //categories.sort((c1, c2) => c1.name.localeCompare(c2.name));
    categories = categories;
    categoryName = '';
  }

  function clearAllChecks() {
    for (const category of Object.values(categories)) {
      for (const item of Object.values(category.items)) {
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
    const pens = createItem('pens', true);
    const wallet = createItem('wallet');
    backpack.items = {
      [pens.id]: pens,
      [wallet.id]: wallet
    };

    let clothes = createCategory('Clothes');
    const socks = createItem('socks', true);
    const shoes = createItem('shoes');
    clothes.items = {
      [socks.id]: socks,
      [shoes.id]: shoes
    };

    categories = {
      [backpack.id]: backpack,
      [clothes.id]: clothes
    };
  }

  function createItem(name, packed = false) {
    return {id: getGuid(), name, packed};
  }

  function deleteCategory(category) {
    //TODO: Warn if contains items.
    delete categories[category.id];
    categories = categories;
  }

  function persist(categories) {
    if (categories) {
      localStorage.setItem('travel-packing', JSON.stringify(categories));
    }
  }

  function restore() {
    const text = localStorage.getItem('travel-packing');
    if (text && text !== '{}') {
      categories = JSON.parse(text);
    } else {
      createDummyData();
    }
  }
</script>

<style>
  .animate {
    display: inline-block;
  }

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
    max-width: 90%;
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

  {#if categories}
    <!-- The bind here is necessary so changes to category in
         the Category component trigger a call to persist here. -->
    {#each Object.values(categories) as category (category.id)}
      <div class="animate" animate:flip={options}>
        <Category
          bind:category
          dnd={dragAndDrop}
          {show}
          on:delete={() => deleteCategory(category)} />
      </div>
    {/each}
  {/if}
</main>
