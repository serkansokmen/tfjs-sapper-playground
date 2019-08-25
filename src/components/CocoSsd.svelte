<script>
  import { onMount, onDestroy } from 'svelte';
  import { writable } from 'svelte/store';
  import * as cocoSsd from '@tensorflow-models/coco-ssd';
  import FileReader from './FileReader.svelte';
  import Loading from '../components/Loading.svelte';

  let innerWidth, innerHeight;

  // const updateMilis = writable(300)
  const predictions = writable([]);
  // const videoConstraints = writable({
  //   width: 640,
  //   height: 480,
  //   facingMode: 'user',
  // })

  const model = writable(null);
  // cocoSsd.load().then(net => {
  //   model.set(net)
  // })

  async function predict(image) {
    console.log(image);
    const results = await $model.detect(image);
    predictions.set(results);
  }
</script>

<svelte:window bind:innerWidth bind:innerHeight />
<Loading loading={!$model}>
  <FileReader on:read={evt => predict(evt.detail)} />

  {#each $predictions as p}
    <pre>{JSON.stringify(p, null, 2)}</pre>
  {/each}
</Loading>
