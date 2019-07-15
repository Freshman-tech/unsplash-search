<script>
  import { onMount } from 'svelte';
  import Search from './Search.svelte';
  import SearchResults from './SearchResults.svelte';
  import LoadingIndicator from './LoadingIndicator.svelte';

  const UNSPLASH_ACCESS_KEY =
    '<your unsplash access key>';

  let searchQuery = '';
  let searchTerm = null;
  let totalPages = null;
  let searchResults = [];
  let nextPage = 1;
  let isLoading = false;

  let observer;
  let target;

  const options = {
    rootMargin: '0px 0px 300px',
    threshold: 0,
  };

  const loadMoreResults = entries => {
    entries.forEach(entry => {
      // If new search or if ongoing search
      if (nextPage === 1 || isLoading) return;

      if (entry.isIntersecting) {
        searchUnsplash();
      }
    });
  };

  onMount(() => {
    observer = new IntersectionObserver(loadMoreResults, options);
    target = document.querySelector('.loading-indicator');
  });

  function handleSubmit() {
    searchTerm = searchQuery.trim();
    searchResults = [];
    totalPages = null;
    nextPage = 1;

    if (!searchTerm) return;

    observer.observe(target);
    searchUnsplash();
  }

  function searchUnsplash() {
    isLoading = true;

    const endpoint =
      `https://api.unsplash.com/search/photos?query=${searchTerm}&page=${nextPage}&per_page=28&client_id=${UNSPLASH_ACCESS_KEY}`;

    fetch(endpoint)
      .then(response => {
        if (!response.ok) {
          throw Error(response.statusText);
        }
        return response.json();
      })
      .then(data => {
        if (data.total === 0) {
          alert("No photos were found for your search query.")
          return;
        }

        searchResults = [...searchResults, ...data.results];
        totalPages = data.total_pages;

        if (nextPage < totalPages) {
          nextPage += 1;
        }
      })
      .catch(() => alert("An error occured!"))
      .finally(() => {
        isLoading = false;

        if (nextPage >= Number(totalPages)) {
          observer.unobserve(target);
        }
      });
  }
</script>

<style>
  .App {
    width: 100%;
    max-width: 1500px;
    padding: 20px;
    margin: 0 auto 50px;
    text-align: center;
  }

  h1 {
    font-size: 50px;
    margin-top: 30px;
    margin-bottom: 30px;
  }
</style>

<main class="App">
  <h1>Unsplash Search App</h1>

  <Search bind:query={searchQuery} handleSubmit={handleSubmit} />

  <SearchResults results={searchResults} />

  <div class="loading-indicator">
    {#if isLoading}
      <LoadingIndicator isLoading={isLoading} />
    {/if}
  </div>
</main>
