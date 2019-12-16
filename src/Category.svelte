<script>
  import {createEventDispatcher} from 'svelte';
  import {flip} from 'svelte/animate';
  import {scale} from 'svelte/transition';
  import {linear} from 'svelte/easing';

  import Dialog from './Dialog.svelte';
  import Item from './Item.svelte';
  import ProgressBar from './ProgressBar.svelte';
  import {getGuid, sortOnName} from './util';

  export let categories;
  export let category;
  export let dnd;
  export let show;

  const dispatch = createEventDispatcher();

  const options = {duration: 700, easing: linear, times: 2};

  let editing = false;
  let hovering = false;
  let itemName = '';
  let items = [];
  let message = '';
  let myDialog = null;

  $: items = Object.values(category.items);
  $: remaining = items.filter(item => !item.packed).length;
  $: total = items.length;
  $: status = `${remaining} of ${total} remaining`;
  $: itemsToShow = sortOnName(items.filter(i => shouldShow(show, i)));

  function addItem() {
    const duplicate = Object.values(categories).some(cat =>
      Object.values(cat.items).some(item => item.name === itemName)
    );
    if (duplicate) {
      message = `The item "${itemName}" already exists.`;
      myDialog.showModal();
      return;
    }

    const {items} = category;
    const id = getGuid();
    items[id] = {id, name: itemName, packed: false};
    //items.sort((item1, item2) => item1.name.localeCompare(item2.name));
    category.items = items;
    itemName = '';
    dispatch('persist');
  }

  function deleteItem(item) {
    delete category.items[item.id];

    // Trigger update.
    category = category;

    dispatch('persist');
  }

  function handleKey(event) {
    if (event.code === 'Enter') event.target.blur();
  }

  function shouldShow(show, item) {
    return (
      show === 'all' ||
      (show === 'packed' && item.packed) ||
      (show === 'unpacked' && !item.packed)
    );
  }

  function spin(node, options) {
    const {easing, times = 1} = options;
    return {
      ...options,
      css(t) {
        const eased = easing(t);
        const degrees = 360 * times; // through which to spin
        return `transform-origin: 50% 50%; transform: scale(${eased}) rotate(${eased *
          degrees}deg);`;
      }
    };
  }
</script>

<section
  in:scale={options}
  out:spin={options}
  class:hover={hovering}
  on:dragenter={() => (hovering = true)}
  on:dragleave={event => {
    const {localName} = event.target;
    if (localName === 'section') hovering = false;
  }}
  on:drop|preventDefault={event => {
    dnd.drop(event, category.id);
    hovering = false;
  }}
  ondragover="return false">
  <ProgressBar percent={(100 * (total - remaining)) / total} />
  <h3>
    {#if editing}
      <input
        bind:value={category.name}
        on:blur={() => (editing = false)}
        on:keypress={handleKey} />
    {:else}
      <span on:click={() => (editing = true)}>{category.name}</span>
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
    <!-- The div inside #each is required because animate
      must appear on a direct child of keyed each block.
      It cannot be applied to a component. -->
    {#each itemsToShow as item (item.id)}
      <div animate:flip>
        <!-- This bind causes the category object to update
          when the item packed value is toggled. -->
        <Item
          bind:item
          {dnd}
          on:delete={() => deleteItem(item)}
          categoryId={category.id} />
      </div>
    {:else}
      <div>This category does not contain any items yet.</div>
    {/each}
  </ul>

  <Dialog title="Category" bind:dialog={myDialog}>
    <div>{message}</div>
  </Dialog>
</section>

<style>
  button,
  input {
    border: solid lightgray 1px;
  }

  button.icon {
    border: none;
  }

  h3 {
    display: flex;
    justify-content: space-between;
    align-items: center;

    margin: 0;
  }

  .hover {
    border-color: orange;
  }

  section {
    --padding: 10px;

    background-color: white;
    border: solid transparent 3px;
    border-radius: var(--padding);
    color: black;
    display: inline-block;
    margin: var(--padding);
    padding: calc(var(--padding) * 2);
    padding-top: var(--padding);
    vertical-align: top;
  }

  section * {
    /* pointer-events: none; */
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
