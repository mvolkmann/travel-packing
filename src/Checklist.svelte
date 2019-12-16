<script>
  import {createEventDispatcher} from 'svelte';
  import {flip} from 'svelte/animate';

  import Category from './Category.svelte';
  import Dialog from './Dialog.svelte';
  import {getGuid, sortOnName} from './util';

  const dispatch = createEventDispatcher();
  const options = {duration: 700};

  let categoryArray = [];
  let categories = {};
  let categoryName;
  let message = '';
  let myDialog = null;
  let show = 'all';

  $: categoryArray = sortOnName(Object.values(categories));

  let dragAndDrop = {
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

      categories = categories; // trigger update
    }
  };

  // Must do this before first call to persist.
  restore();

  // Any time categories changes, persist it to localStorage.
  $: if (categories) persist();

  function addCategory() {
    const duplicate = Object.values(categories).some(
      cat => cat.name === categoryName
    );
    if (duplicate) {
        message = `The category "${categoryName}" already exists.`;
      myDialog.showModal();
      return;
    }

    const id = getGuid();
    categories[id] = {id, name: categoryName, items: {}};
    //categories.sort((c1, c2) => c1.name.localeCompare(c2.name));
    categories = categories; // trigger update
    categoryName = ''; // clear input
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
    return {id: getGuid(), name, items: {}};
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
    if (Object.values(category.items).length) {
      message = 'This category is not empty.';
      myDialog.showModal();
      return;
    }

    delete categories[category.id];
    categories = categories;
  }

  function persist() {
    localStorage.setItem('travel-packing', JSON.stringify(categories));
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

<section>
  <header>
    <form on:submit|preventDefault={addCategory}>
      <label>
        New Category
        <input bind:value={categoryName} />
      </label>
      <button disabled={!categoryName}>Add Category</button>
      <button
        class="logout-btn"
        on:click|preventDefault={() => dispatch('logout')}>
        Log Out
      </button>
    </form>
    <p>
      Suggested categories include Backpack, Clothes,
      <br />
      Last Minute, Medicines, Running Gear, and Toiletries.
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
  </header>

  <div class="categories">
    <!-- The bind here is necessary so changes to category in
        the Category component trigger a call to persist here. -->
    {#each categoryArray as category (category.id)}
      <div class="animate" animate:flip={options}>
        <Category
          bind:category
          {categories}
          dnd={dragAndDrop}
          {show}
          on:delete={() => deleteCategory(category)}
          on:persist={persist} />
      </div>
    {/each}
  </div>

  <Dialog title="Categories" bind:dialog={myDialog}>
    <div>{message}</div>
  </Dialog>
</section>

<style>
  .animate {
    display: inline-block;
  }

  .categories {
    display: inline-flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .clear {
    margin-left: 30px;
  }

  input[type='radio'] {
    --size: 24px;
    height: var(--size);
    width: var(--size);
    margin-left: 10px;
  }

  .logout-btn {
    position: absolute;
    right: 20px;
    top: 20px;
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
    margin-bottom: -3px;
    margin-right: 5px;
  }

  section {
    display: flex;
    flex-direction: column;
    align-items: center;

    font-size: 24px;
    margin-top: 1em;
    /* max-width: 90%; */
  }
</style>
