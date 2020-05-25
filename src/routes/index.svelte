<script>
  import pix2pix from '../lib/pix2pix';
  import { mapRange } from '../lib/utils';
  import { onMount } from 'svelte';
  import CircularInput from '../components/CircularInput.svelte';
  import Loading from '../components/Loading.svelte';

  let isModelLoaded = false;
  let isTrackingEnabled = false;
  let size = 256;
  let output;
  let model;

  onMount(() => {
    model = pix2pix('/models/bakarlar_002_BtoA.pict', () => {
      isModelLoaded = true;
    });
  });

  async function handleChange(evt) {
    const stage = evt.detail;
    const canvas = stage.toCanvas({
      x: 0,
      y: 0,
      width: size,
      height: size,
      pixelRatio: 1,
    });

    model.transfer(canvas, result => {
      output = result.src;
    });
  }
</script>

<style>
  .guide {
    position: absolute;
    top: 0;
    left: 0;
  }
</style>

<svelte:head>
  <title>Home</title>

  <script src="https://unpkg.com/konva@4.0.0/konva.min.js">

  </script>
</svelte:head>
<Loading loading={!isModelLoaded}>
  <CircularInput on:change={handleChange} {size} />
  {#if output}
    <img src={output} alt="Output" width={size} height={size} />
  {/if}
  <img src="images/bakarlar/guide.png" alt="guide" class="guide" />
  <input type="checkbox" bind:checked={isTrackingEnabled} />
</Loading>
