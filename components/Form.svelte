<script>
  import { createEventDispatcher } from 'svelte';

  let tokenInput = '';

  const dispatch = createEventDispatcher();

  function clearTokenInput(event) {
    event.preventDefault();
    tokenInput = '';
  }

  function saveToken(event) {
    event.preventDefault();
    dispatch('saveToken', tokenInput);
  }
</script>

<div class="flex flex-grow justify-center items-center">
  <form action="" class="w-1/2">
    <input
      bind:value={tokenInput}
      placeholder="Paste a Netlify API token"
      class="w-full p-4 rounded" />
    <button
      class="bg-green-600 text-white p-2 w-1/4 my-2 rounded"
      type="submit"
      on:click={saveToken}>
      Save Token
    </button>
    <button
      class="bg-red-600 text-white p-2 w-1/4 my-2 rounded"
      type="clear"
      on:click={clearTokenInput}>
      Clear Token
    </button>
  </form>
</div>
