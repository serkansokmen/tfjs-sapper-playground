<script>
  export let loading;
  export let label = 'Loading';
</script>

<style>
  .loading {
    display: grid;
    align-items: center;
    justify-items: start;
    grid-template-columns: 0.25fr 1.75fr;
    grid-template-rows: 0.25fr 1.75fr;
    grid-gap: 1em;
  }  
</style>

{#if loading}
<div class="loading">
  <img height="12em" src="images/spinner.svg" alt="Loading spinner"/>
  <span>{label}</span>
</div>
{:else}
  <slot />
{/if}