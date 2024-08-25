<script lang="ts">
  import { onMount } from 'svelte';
  import { debounce } from 'lodash-es';

  interface Product {
    id: number;
    title: string;
    price: number;
    category: string;
    rating: number;
    stock: number;
    brand: string;
    description: string;
  }

  let products: Product[] = [];
  let filteredProducts: Product[] = [];
  let searchTerm = '';
  let sortColumn = 'id';
  let sortDirection: 'asc' | 'desc' = 'asc';
  let categories: string[] = [];
  let selectedCategory = 'all';
  let currentPage = 1;
  let itemsPerPage = 10;
  let totalItems = 0;

  $: totalPages = Math.ceil(totalItems / itemsPerPage);

  let searchTimeout: number;

  onMount(async () => {
    await fetchCategories();
    await fetchProducts();
  });

  async function fetchProducts() {
    const skip = (currentPage - 1) * itemsPerPage;
    let url = `https://dummyjson.com/products`;
    
    if (searchTerm) {
      url += `/search?q=${searchTerm}&limit=${itemsPerPage}&skip=${skip}`;
    } else {
      url += `?limit=${itemsPerPage}&skip=${skip}`;
    }

    url += `&select=id,title,price,category,rating,stock,brand,description`;

    const response = await fetch(url);
    const data = await response.json();
    products = data.products;
    totalItems = data.total;
    filteredProducts = products;
  }

  const debouncedSearch = debounce(async () => {
    currentPage = 1;
    await fetchProducts();
  }, 300);

  function handleSearch() {
    debouncedSearch();
  }

  function handleCategoryChange() {
    currentPage = 1;
    applyFilters();
  }

  function applyFilters() {
    filteredProducts = products.filter(product => 
      selectedCategory === 'all' || product.category === selectedCategory
    );
    totalItems = filteredProducts.length;
  }

  async function handlePageChange(newPage: number) {
    currentPage = newPage;
    await fetchProducts();
  }

  async function fetchCategories() {
    const response = await fetch('https://dummyjson.com/products/category-list');
    categories = await response.json();
    categories.unshift('all');
  }

  async function handleSort(column: string) {
    if (sortColumn === column) {
      sortDirection = sortDirection === 'asc' ? 'desc' : 'asc';
    } else {
      sortColumn = column;
      sortDirection = 'asc';
    }
    await fetchProducts();
  }
</script>

<div class="mb-4">
  <input
    type="text"
    placeholder="Search products..."
    bind:value={searchTerm}
    on:input={handleSearch}
    class="p-2 border rounded"
  />
  <select bind:value={selectedCategory} on:change={handleCategoryChange} class="ml-2 p-2 border rounded">
    {#each categories as category}
      <option value={category}>{category}</option>
    {/each}
  </select>
</div>

<div class="overflow-x-auto">
  <table class="min-w-full bg-white">
    <thead>
      <tr>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('id')}>ID</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('title')}>Title</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('price')}>Price</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('category')}>Category</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('rating')}>Rating</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('stock')}>Stock</th>
        <th class="px-4 py-2 cursor-pointer" on:click={() => handleSort('brand')}>Brand</th>
        <th class="px-4 py-2">Description</th>
      </tr>
    </thead>
    <tbody>
      {#each filteredProducts as product (product.id)}
        <tr>
          <td class="border px-4 py-2">{product.id}</td>
          <td class="border px-4 py-2">{product.title}</td>
          <td class="border px-4 py-2">${product.price.toFixed(2)}</td>
          <td class="border px-4 py-2">{product.category}</td>
          <td class="border px-4 py-2">{product.rating.toFixed(1)}</td>
          <td class="border px-4 py-2">{product.stock}</td>
          <td class="border px-4 py-2">{product.brand}</td>
          <td class="border px-4 py-2">
            <div class="truncate max-w-xs" title={product.description}>
              {product.description}
            </div>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>

<div class="mt-4 flex justify-between items-center">
  <div>
    Showing {(currentPage - 1) * itemsPerPage + 1} - {Math.min(currentPage * itemsPerPage, totalItems)} of {totalItems} items
  </div>
  <div>
    <button
      class="px-4 py-2 border rounded mr-2"
      disabled={currentPage === 1}
      on:click={() => handlePageChange(currentPage - 1)}
    >
      Previous
    </button>
    <span class="mx-2">Page {currentPage} of {totalPages}</span>
    <button
      class="px-4 py-2 border rounded ml-2"
      disabled={currentPage === totalPages}
      on:click={() => handlePageChange(currentPage + 1)}
    >
      Next
    </button>
  </div>
</div>