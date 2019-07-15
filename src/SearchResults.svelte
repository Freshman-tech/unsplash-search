<script>
  import PhotoCard from './PhotoCard.svelte';

  export let results;
</script>

<style>
  .search-results {
    list-style: none;
    display: grid;
    gap: 40px 20px;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  }
</style>

<ul class="search-results">
  {#each results as result (result.id)}
    <PhotoCard photo={result} />
  {/each}
</ul>
