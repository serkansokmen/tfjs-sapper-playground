<script>
  export let segment;
</script>

<style>
  .selected {
    position: relative;
  }
  .selected::after {
    position: absolute;
    content: '';
    width: calc(100% - 1em);
    height: 2px;
    background-color: rgb(255, 62, 0);
    display: block;
    bottom: -1px;
  }
</style>

<nav class="navbar" role="navigation" aria-label="Main navigation">
  <div class="navbar-brand">
    <a class="navbar-item" href="https://bulma.io">
      <img src="logo-192.png" height="28" alt="Logo"/>
    </a>

    <button role="button" class="navbar-burger burger" aria-label="menu" aria-expanded="false" data-target="navbarBasicExample">
      <span aria-hidden="true"></span>
      <span aria-hidden="true"></span>
      <span aria-hidden="true"></span>
    </button>
  </div>

  <div class="navbar-menu">
    <div class="navbar-start">
      <a class={segment===undefined ? 'navbar-item selected' : 'navbar-item' } href=".">Mobilenet</a>
      <a class={segment==='video' ? 'navbar-item selected' : 'navbar-item' } href="video">Custom Classifier</a>
      <a class={segment==='about' ? 'navbar-item selected' : 'navbar-item' } href="about" rel="prefetch">About</a>
    </div>
  </div>
</nav>
