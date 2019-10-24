<script>
  import { onMount, onDestroy } from 'svelte';
  import NetlifyAPI from 'netlify';

  import Sites from './Sites.svelte';
  import Form from './Form.svelte';

  let sites = [];
  const netlifyClient = new NetlifyAPI(loadToken());
  let updateTimer;

  function loadToken() {
    return localStorage.getItem('netlify-api-key');
  }

  async function saveToken(event) {
    event.preventDefault();

    localStorage.setItem('netlify-api-key', event.detail);
    netlifyClient.accessToken = loadToken();
    sites = await fetchSites();
    startUpdateTimer();
  }

  function clearToken(event) {
    event.preventDefault();

    localStorage.removeItem('netlify-api-key');
    netlifyClient.accessToken = loadToken();
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

  async function fetchSites() {
    let data = await netlifyClient.listSites();
    data = data.map(site => ({
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

  onMount(async () => {
    if (netlifyClient.accessToken) {
      sites = await fetchSites();
      startUpdateTimer();
    }
  });

  onDestroy(() => {
    clearInterval(updateTimer);
  });
</script>

<header class="bg-blue-400 text-white sticky top-0 z-20">
  <div class="flex items-center p-2">
    <h1 class="text-2xl font-serif ml-2">
      <a href="/" class="hover:text-brand-light">netlify dashboard</a>
    </h1>
    <div class="flex-grow" />
    <div class="flex">
      {#if netlifyClient.accessToken}
        <button class="bg-red-600 text-white p-2 rounded" on:click={clearToken}>
          Clear Token
        </button>
      {/if}
    </div>
  </div>
</header>

{#if !netlifyClient.accessToken}
  <Form on:saveToken={saveToken} />
{:else if sites.length}
  <Sites {sites} />
{:else}
  <div class="flex w-full h-screen justify-center items-center">
    <p class="text-4xl">LOADING DATA</p>
  </div>
{/if}
