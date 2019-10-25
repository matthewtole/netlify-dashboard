<script>
  import { formatDistance } from 'date-fns';

  export let sites = [];
  export let view = 'grid';
</script>

{#if view === 'grid'}
  <div class="flex flex-wrap p-2">
    {#each sites as site}
      <div class="w-1/5">
        <div class="shadow rounded m-2 p-2 bg-white">
          <a href={site.adminUrl}>
            <img src={site.screenshot} alt={`Screenshot of ${site.url}`} />
          </a>
          Last published {formatDistance(site.lastPublished, new Date())} ago
        </div>
      </div>
    {/each}
  </div>
{:else}
  <div class="flex flex-col m-2">
    {#each sites as site}
      <div class="w--full mx-1 my-2">
        <div class="shadow rounded p-2 bg-white flex">
          <a href={site.adminUrl} class="w-48 mr-4">
            <img src={site.screenshot} alt={`Screenshot of ${site.url}`} />
          </a>
          <div class="flex flex-col justify-center">
            <p class="text-2xl">
              <a href={site.adminUrl}>{site.name}</a>
            </p>
            <p>
              <a href={site.url} class="text-cta hover:underline">{site.url}</a>
            </p>
            <p>
              Last published {formatDistance(site.lastPublished, new Date())}
              ago
            </p>
          </div>
        </div>
      </div>
    {/each}
  </div>
{/if}
