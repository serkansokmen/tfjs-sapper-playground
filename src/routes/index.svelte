<svelte:head>
  <title>Image Classifier</title>
</svelte:head>

<script>
  import { onMount } from 'svelte';
  import Dropzone from '../components/Dropzone.svelte';
  import Loading from '../components/Loading.svelte';

  let isModelLoaded = false;
  let isTrackingEnabled = false;
  let isDropzoneReady = false;
  let net;
  let result = '';

  onMount(async () => {
    // Load the model.
    const tf = await import('@tensorflow/tfjs');
    const mobilenet = await import('@tensorflow-models/mobilenet');
    net = await mobilenet.load();
    isModelLoaded = true;
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
  }
  
  function handleDropzoneReady(evt) {
    isDropzoneReady = true;
  }

  async function handleFileAdded(evt) {
    // Make a prediction through the model on our image.
    result = await net.classify(evt.detail);
    console.log(result);
  }

  function handleReset(evt) {
    result = '';
  }
</script>

<style>
  section {
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr;
    grid-template-areas: 'screen debug';
    grid-gap: 2em;
    align-items: stretch;
    justify-items: stretch;
  }

  .screen {
    grid-area: screen;
  }
  section pre {
    grid-area: debug;
  }
</style>

<Loading loading="{!isModelLoaded}" label="Loading model...">
  <section>
    <div class="screen">
      <Dropzone
        on:ready={handleDropzoneReady} 
        on:added={handleFileAdded}
        on:reset={handleReset}/>
    </div>
    <pre>{JSON.stringify(result, null, 2)}</pre>
  </section>
</Loading>
