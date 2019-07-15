<script>
  export let query;
  export let handleSubmit;
</script>

<style>
  .search-input {
    width: 100%;
    max-width: 800px;
    border-radius: 4px;
    border: 1px solid #ccc;
    padding: 10px 20px;
    font-size: 20px;
    margin-bottom: 50px;
  }
</style>

<div class="search">
  <form class="search-form" on:submit|preventDefault={handleSubmit}>
    <input bind:value={query} class="search-input" type="search"
    placeholder="Search Unsplash's library of over 1 million photos" />
  </form>
</div>
