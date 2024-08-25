<script lang="ts">
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  let ApexCharts;

  let salesChart: Chart;
  let userChart: any;

  onMount(async () => {
    ApexCharts = (await import('apexcharts')).default;

    // Sales Chart (Chart.js)
    const salesCtx = document.getElementById('salesChart') as HTMLCanvasElement;
    salesChart = new Chart(salesCtx, {
      type: 'line',
      data: {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [{
          label: 'Sales',
          data: [12, 19, 3, 5, 2, 3],
          borderColor: 'rgb(75, 192, 192)',
          tension: 0.1
        }]
      }
    });

    // User Growth Chart (ApexCharts)
    const userOptions = {
      chart: {
        type: 'area',
        height: 250
      },
      series: [{
        name: 'Users',
        data: [30, 40, 45, 50, 49, 60, 70, 91, 125]
      }],
      xaxis: {
        categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep']
      }
    };
    userChart = new ApexCharts(document.querySelector("#userChart"), userOptions);
    userChart.render();
  });
</script>

<svelte:head>
  <script src="https://cdn.jsdelivr.net/npm/apexcharts" defer></script>
</svelte:head>

<div class="w-full">
  <h1 class="text-3xl font-bold text-gray-900 mb-8">Dashboard</h1>
  
  <!-- Statistics Cards -->
  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mb-8">
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-lg font-semibold text-gray-700">Total Users</h3>
      <p class="text-3xl font-bold text-blue-600">10,245</p>
    </div>
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-lg font-semibold text-gray-700">Revenue</h3>
      <p class="text-3xl font-bold text-green-600">$45,678</p>
    </div>
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-lg font-semibold text-gray-700">Conversion Rate</h3>
      <p class="text-3xl font-bold text-purple-600">3.2%</p>
    </div>
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-lg font-semibold text-gray-700">Avg. Session Duration</h3>
      <p class="text-3xl font-bold text-orange-600">2m 35s</p>
    </div>
  </div>
  
  <!-- Charts -->
  <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-xl font-semibold text-gray-800 mb-4">Sales Overview</h3>
      <canvas id="salesChart"></canvas>
    </div>
    <div class="bg-white rounded-lg shadow p-6">
      <h3 class="text-xl font-semibold text-gray-800 mb-4">User Growth</h3>
      <div id="userChart"></div>
    </div>
  </div>
</div>