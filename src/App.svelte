<script>
  import Category from './Category.svelte';
	export let categoryName;

  let categories = [
    {
      name: 'Clothes',
      items: [
        {name: 'socks', packed: true},
        {name: 'shoes', packed: false}
      ]
    },
    {
      name: 'Backpack',
      items: [
        {name: 'pens', packed: true},
        {name: 'wallet', packed: false}
      ]
    }
  ];

  function addCategory() {
    categories.push({name: categoryName, items: []});
    categories.sort((c1, c2) => c1.name.localeCompare(c2.name));
    categories = categories;
  }
</script>

<style>
  button:disabled {
    color: lightgray;
  }

  label {
    display: inline-block;
  }
</style>

<main>
	<h1>Travel Packing Checklist</h1>

  <div>
    <label>
      New Category
      <input bind:value={categoryName} />
    </label>
    <button disabled={!categoryName} on:click={addCategory}>Add Category</button>
  </div>

  {#each categories as category}
    <Category category={category} />
  {/each}
</main>
