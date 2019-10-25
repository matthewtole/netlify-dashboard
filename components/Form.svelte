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
  <form action="" class="w-1/2 flex bg-white rounded">
    <input
      bind:value={tokenInput}
      placeholder="Paste a Netlify API token"
      class="w-full p-4 rounded" />
    <button
      class="text-gray-800 px-4 hover:text-green-700"
      class:opacity-50={!tokenInput}
      type="submit"
      on:click={saveToken}>
      SAVE
    </button>
  </form>
</div>
