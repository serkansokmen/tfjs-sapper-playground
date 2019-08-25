<script>
  import { onMount, onDestroy, createEventDispatcher } from 'svelte';
  export let size;

  const dispatch = createEventDispatcher();

  let stage;
  let eyeLayer;
  let eyeRing;
  let eyePupil;
  let mouseVec = {
    x: size / 2,
    y: size / 2,
  };

  onMount(() => {
    stage = new window.Konva.Stage({
      container: 'stage',
      width: size,
      height: size,
    });
    stage.on('mousemove touchmove', handleTrack);
    eyeLayer = new window.Konva.Layer();
    eyeRing = new Konva.Circle({
      x: size / 2,
      y: size / 2,
      // radius: size / 3.6,
      radius: 158,
      fill: 'white',
      stroke: 'black',
      strokeWidth: 2,
    });
    // [Konva.Filters.Noise, Konva.Filters.Blur]
    eyePupil = new Konva.Circle({
      x: size / 2,
      y: size / 2,
      // radius: size / 8,
      radius: 54,
      fill: 'black',
      stroke: 'black',
      strokeWidth: 0,
    });
    eyeLayer.add(eyeRing);
    eyeLayer.add(eyePupil);
    stage.add(eyeLayer);
  });

  $: if (eyeLayer) {
    eyePupil.x(mouseVec.x);
    eyePupil.y(mouseVec.y);
    eyeLayer.batchDraw();
  }

  function handleTrack(evt) {
    const pos = stage ? stage.getPointerPosition() : mouseVec;
    mouseVec = {
      x: pos.x,
      y: pos.y,
    };
    dispatch('change', stage);
  }
</script>

<style>
  section {
    position: relative;
  }
  #stage {
    position: relative;
    z-index: 40;
  }
</style>

<section>
  <div id="stage" />
</section>
