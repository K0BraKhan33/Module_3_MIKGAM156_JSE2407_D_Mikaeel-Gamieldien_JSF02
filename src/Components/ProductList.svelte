<script>
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  // Define writable stores for items, filtered items, loading state, sorting, and categories
  const items = writable([]);
  const filteredItems = writable([]);
  const isLoading = writable(true);
  const sortPrice = writable('');
  const sortType = writable('');
  const categories = writable([]);

  /**
   * Initialize sorting and filtering from URL parameters.
   */
  function initializeFiltersFromUrl() {
    const urlParams = new URLSearchParams(window.location.hash.slice(1));
    const initialSortPrice = urlParams.get('sortPrice') || '';
    const initialSortType = urlParams.get('sortType') || '';

    sortPrice.set(initialSortPrice);
    sortType.set(initialSortType);
  }

  /**
   * Fetch product data and categories on component mount.
   */
  onMount(async () => {
    try {
      // Fetch products
      const response = await fetch('https://fakestoreapi.com/products');
      const products = await response.json();
      items.set(products);
      filteredItems.set(products);

      // Fetch categories
      const categoriesResponse = await fetch('https://fakestoreapi.com/products/categories');
      const categoryList = await categoriesResponse.json();
      categories.set(categoryList);

      // Initialize sorting and filtering from URL
      initializeFiltersFromUrl();
    } catch (err) {
      console.error('Error fetching data:', err);
    } finally {
      isLoading.set(false);
    }
  });

  // Reactively apply filtering and sorting
  $: {
    let currentItems = $items;

    // Apply filtering by category
    if ($sortType) {
      currentItems = currentItems.filter(item => item.category === $sortType);
    }

    // Apply sorting
    if ($sortPrice) {
      currentItems.sort((a, b) => {
        switch ($sortPrice) {
          case 'priceAsc':
            return a.price - b.price;
          case 'priceDesc':
            return b.price - a.price;
          case 'titleAsc':
            return a.title.localeCompare(b.title);
          case 'titleDesc':
            return b.title.localeCompare(a.title);
          default:
            return 0;
        }
      });
    }

    filteredItems.set(currentItems);
  }

  /**
   * Update URL parameters when sort options change.
   */
  function handleSortChange() {
    const priceSort = $sortPrice;
    const typeSort = $sortType;

    const urlParams = new URLSearchParams({
      sortPrice: priceSort,
      sortType: typeSort
    });

    window.history.replaceState({}, '', `#?${urlParams.toString()}`);
  }

  /**
   * Reset sorting and filtering to default values.
   */
  function resetFilters() {
    sortPrice.set('');
    sortType.set('');
    handleSortChange();
  }

  // Track if sorting or filtering is set to default
  $: isDefaultSort = !$sortPrice && !$sortType;
</script>

<div class="bg-green-800 min-h-screen p-4">
  <!-- Header Section -->
  <header class="bg-green-800 p-4 flex justify-between items-center">
    <!-- Site Title -->
    <div class="flex items-center">
      <h1 class="text-white text-[4vw] font-bold">GoodMarting</h1>
    </div>
  </header>

  <!-- Sorting and Filtering -->
  <div class="bg-green-300 flex p-max space-x-4">
    <div>
      <label for="sortPrice" class="block text-sm font-medium text-gray-700">Sort by Title or Price</label>
      <select id="sortPrice" bind:value={$sortPrice} on:change={handleSortChange}
        class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md">
        <option value="">Select</option>
        <option value="priceAsc">Price: Low to High</option>
        <option value="priceDesc">Price: High to Low</option>
        <option value="titleAsc">Title: A to Z</option>
        <option value="titleDesc">Title: Z to A</option>
      </select>
    </div>
    <div>
      <label for="sortType" class="block text-sm font-medium text-gray-700">Sort by Type</label>
      <select id="sortType" bind:value={$sortType} on:change={handleSortChange}
        class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md">
        <option value="">Select</option>
        {#each $categories as category}
          <option value={category}>{category}</option>
        {/each}
      </select>
    </div>
    {#if !isDefaultSort}
      <button on:click={resetFilters} class="mt-6 py-2 px-4 bg-red-500 text-white rounded-md">Reset Filters</button>
    {/if}
  </div>

  <!-- Product List -->
  <div>
    {#if $isLoading}
      <div class="flex justify-center items-center h-full">
        <p class="border-gray-300 text-[5vw]">Loading...</p>
      </div>
    {:else}
      <ul class="flex flex-wrap -mx-4">
        {#each $filteredItems as item}
          <li class="flex flex-col w-full md:w-1/2 lg:w-1/4 p-4 min-h-[60vw] sm:min-h-[25vw] md:min-h-[20vw] lg:min-h-[15vw] cursor-pointer">
            <a href={`/#/about?id=${item.id}&sortPrice=${$sortPrice}&sortType=${$sortType}`} class="block h-full">
              <div class="border-2 border-green-800 bg-green-300 p-4 rounded-lg h-full flex flex-col">
                <img src="{item.image}" alt="{item.title}" class="w-full max-h-[35vw] sm:max-h-[20vw] md:max-h-[30vw] lg:max-h-[25vw] object-contain mb-4">
                <div class="flex flex-col flex-grow">
                  <div class="mb-4">
                    <h2 class="font-bold text-[4vw] sm:text-[3vw] md:text-[2.5vw] lg:text-[1.5vw]">{item.title}</h2>
                  </div>
                  <div class="flex flex-col mb-4">
                    <p class="text-gray-700 text-[4vw] sm:text-[3vw] md:text-[2.5vw] lg:text-[2vw]">{'$' + item.price}</p>
                    <p class="text-[4vw] lg:text-[1.5vw] md:text-[2vw] sm:text-[3vw] text-black mb-0">Category: {item.category}</p>
                    <div class="flex items-center">
                      <span class="text-[4vw] lg:text-[1.5vw] md:text-[2vw] sm:text-[3vw] text-gray-800 mr-2">Rating:</span>
                      <div class="flex items-center">
                        {#if item.rating && item.rating.rate !== undefined}
                          {#each { length: Math.floor(item.rating.rate) } as _}
                            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-yellow-500" fill="currentColor" viewBox="0 0 24 24">
                              <path d="M12 17.27l6.18 3.85-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.35-1.64 7.03L12 17.27z"/>
                            </svg>
                          {/each}
                          {#each { length: Math.ceil(item.rating.rate - Math.floor(item.rating.rate)) } as _}
                            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-yellow-500" fill="currentColor" viewBox="0 0 24 24">
                              <path d="M12 17.27l6.18 3.85-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.35-1.64 7.03L12 17.27z"/>
                            </svg>
                          {/each}
                          {#each { length: 5 - Math.ceil(item.rating.rate) } as _}
                            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
                              <path d="M12 17.27l6.18 3.85-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.35-1.64 7.03L12 17.27z"/>
                            </svg>
                          {/each}
                        {/if}
                      </div>
                    </div>
                    <span class="text-[4vw] lg:text-[1.5vw] md:text-[2vw] sm:text-[3vw] text-gray-800">
                      {item.rating ? `(${item.rating.count} reviews)` : 'No reviews'}
                    </span>
                  </div>
                </div>
              </div>
            </a>
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</div>
