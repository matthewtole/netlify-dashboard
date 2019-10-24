<script>
  import { formatDistance } from 'date-fns';

  export let sites = [];
</script>

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
