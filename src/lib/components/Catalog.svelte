<script lang="ts">
    import { onMount } from 'svelte';
  
    interface Product {
      id: number;
      title: string;
      price: number;
      description: string;
      image: string;
      rating: {
        rate: number;
        count: number;
      };
    }
  
    let products: Product[] = [];
    let loading = true;
  
    onMount(async () => {
      try {
        const response = await fetch('https://fakestoreapi.com/products');
        products = await response.json();
        console.log('Products loaded:', products); // Debug log
      } catch (error) {
        console.error('Error loading products:', error);
      } finally {
        loading = false;
      }
    });
  
    function truncate(str: string, n: number) {
      return (str.length > n) ? str.slice(0, n-1) + '...' : str;
    }
  </script>
  
  <div class="container mx-auto px-4 py-8">
    <h1 class="text-2xl font-semibold mb-8">Product Catalog</h1>
    
    {#if loading}
      <div class="flex justify-center items-center h-64">
        <div class="loader"></div>
      </div>
    {:else}
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
        {#each products.slice(0, 16) as product (product.id)}
          <div class="bg-white rounded-lg shadow-md overflow-hidden flex flex-col border border-gray-200">
            <div class="relative pt-[100%] bg-gray-100">
              <img 
                src={product.image} 
                alt={product.title} 
                class="absolute top-0 left-0 w-full h-full object-contain p-4"
              />
            </div>
            
            <div class="p-4 flex-grow flex flex-col">
              <h2 class="text-sm font-medium mb-2 h-10 overflow-hidden">{truncate(product.title, 50)}</h2>
              <p class="text-xs text-gray-500 mb-2 h-8 overflow-hidden">{truncate(product.description, 60)}</p>
              <div class="mt-auto">
                <div class="flex items-center mb-2">
                  <span class="text-yellow-500 mr-1">★</span>
                  <span class="text-sm text-gray-600">{product.rating.rate.toFixed(1)} ({product.rating.count})</span>
                </div>
                <div class="flex justify-between items-center">
                  <span class="text-lg font-bold text-red-600">Rp{Math.round(product.price * 15000).toLocaleString('id-ID')}</span>
                  <span class="text-sm text-gray-500">Jakarta</span>
                </div>
              </div>
            </div>
          </div>
        {/each}
      </div>
    {/if}
  </div>
  
  <style>
    .loader {
      border: 5px solid #f3f3f3;
      border-top: 5px solid #3498db;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      animation: spin 1s linear infinite;
    }
  
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
  </style>