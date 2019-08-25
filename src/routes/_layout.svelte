<script>
  import Nav from '../components/Nav.svelte';

  export let segment;
</script>

<style>
  main {
    position: relative;
    max-width: 56em;
    background-color: white;
    padding: 0;
    margin: 0 auto;
    box-sizing: border-box;
  }
</style>

<svelte:head />
<Nav {segment} />

<main>
  <slot />
</main>
