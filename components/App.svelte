<script>
  import { onMount, onDestroy } from 'svelte';
  import axios from 'axios';

  import Sites from './Sites.svelte';
  import Form from './Form.svelte';

  let sites = [];
  let token = loadToken();
  let updateTimer;
  let requestsRemaining;
  let view = 'list';

  function loadToken() {
    return localStorage.getItem('netlify-api-key');
  }

  async function saveToken(event) {
    event.preventDefault();

    localStorage.setItem('netlify-api-key', event.detail);
    token = loadToken();
    sites = await fetchSites();
    startUpdateTimer();
  }

  function clearToken(event) {
    event.preventDefault();

    localStorage.removeItem('netlify-api-key');
    token = loadToken();
    sites = [];
  }

  function generatePlaceholderScreenshot(site) {
    return `https://placeholder.pics/svg/960x600/DEDEDE/555555/${site.url}`;
  }

  function startUpdateTimer() {
    clearInterval(updateTimer);
    updateTimer = setInterval(async () => {
      sites = await fetchSites();
    }, 60 * 1000);
  }

  async function refresh() {
    clearInterval(updateTimer);
    sites = await fetchSites();
    startUpdateTimer();
  }

  async function fetchSites() {
    const res = await axios.get('https://api.netlify.com/api/v1/sites', {
      headers: { Authorization: `Bearer ${token}` },
    });
    requestsRemaining = parseInt(res.headers['x-ratelimit-remaining'], 10);
    const data = res.data.map(site => ({
      name: site.name,
      screenshot: site.screenshot_url || generatePlaceholderScreenshot(site),
      url: site.url,
      adminUrl: site.admin_url,
      lastPublished: new Date(site.published_deploy.published_at),
    }));
    data.sort((site1, site2) => {
      if (site1.lastPublished < site2.lastPublished) {
        return 1;
      }
      if (site1.lastPublished > site2.lastPublished) {
        return -1;
      }
      return 0;
    });
    return data;
  }

  function switchView() {
    view = view === 'grid' ? 'list' : 'grid';
  }

  onMount(async () => {
    if (token) {
      sites = await fetchSites();
      startUpdateTimer();
    }
  });

  onDestroy(() => {
    clearInterval(updateTimer);
  });
</script>

<header class="bg-brand text-white sticky top-0 z-20">
  <div class="flex items-center p-2">
    <h1 class="text-2xl font-serif ml-2">
      <a href="/" class="hover:text-brand-light">netlify dashboard</a>
    </h1>
    <div class="flex-grow" />
    <div class="flex">
      {#if requestsRemaining}
        <span class="p-2 text-brand-light">{requestsRemaining}</span>
      {/if}
      {#if token}
        <button class="bg-red-600 text-white p-2 rounded" on:click={clearToken}>
          Clear Token
        </button>
        <button class="bg-blue-600 text-white p-2 rounded" on:click={refresh}>
          Refresh
        </button>
        <button
          class="bg-blue-600 text-white p-2 rounded"
          on:click={switchView}>
          Switch View
        </button>
      {/if}
    </div>
  </div>
</header>

{#if !token}
  <Form on:saveToken={saveToken} />
{:else if sites.length}
  <Sites {sites} {view} />
{:else}
  <div class="flex w-full h-screen justify-center items-center">
    <p class="text-4xl">LOADING DATA</p>
  </div>
{/if}
