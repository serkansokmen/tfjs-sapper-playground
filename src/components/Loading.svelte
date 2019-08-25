<script>
  export let loading;
  export let label = 'Loading';
</script>

{#if loading}
  <span>{label}</span>
{:else}
  <slot />
{/if}
