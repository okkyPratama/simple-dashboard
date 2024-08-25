<script lang="ts">
  import '../app.css'
  import Sidebar from '$lib/components/Sidebar.svelte';
  import Navbar from '$lib/components/Navbar.svelte';
</script>

<div class="flex h-screen bg-gray-100">
  <Sidebar />
  <div class="flex-1 flex flex-col overflow-hidden">
    <Navbar />
    <main class="flex-1 overflow-x-hidden overflow-y-auto bg-gray-200 p-6">
      <slot />
    </main>
  </div>
</div>