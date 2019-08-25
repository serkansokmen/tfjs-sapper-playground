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
    margin: 4rem auto;
    box-sizing: border-box;
  }
</style>

<svelte:head>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.5/css/bulma.min.css"/>
  <script defer src="https://use.fontawesome.com/releases/v5.3.1/js/all.js"></script>
</svelte:head>

<Nav {segment} />

<main>
  <slot />
</main>
