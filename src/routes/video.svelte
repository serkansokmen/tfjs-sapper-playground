<svelte:head>
  <title>Video Classifier</title>
</svelte:head>

<script>
  import { onMount, onDestroy } from 'svelte';
  import Loading from '../components/Loading.svelte';

  let webcamElement;
  let isModelLoaded;
  let net;
  let classifier;
  let result;
  let isMounted;

  const classes = ['A', 'B', 'C'];

  onMount(async () => {
    isMounted = true;

    // Load the model.
    const tf = await import('@tensorflow/tfjs');
    const mobilenet = await import('@tensorflow-models/mobilenet');
    const knnClassifier = await import('@tensorflow-models/knn-classifier');
    net = await mobilenet.load();
    classifier = await knnClassifier.create();
    isModelLoaded = true;
    console.log('Sucessfully loaded model');

    await setupWebcam();

    while (isMounted) {
      if (classifier.getNumClasses() > 0) {
        // Get the activation from mobilenet from the webcam.
        const activation = net.infer(webcamElement, 'conv_preds');
        // Get the most likely class and confidences from the classifier module.
        const prediction = await classifier.predictClass(activation);
        
        result = {
          prediction: classes[prediction.classIndex],
          probability: prediction.confidences[prediction.classIndex],
        }
        console.log(prediction, result)
      }
      
      await tf.nextFrame();
    }
  });

  onDestroy(() => {
    isMounted = false;
  });

  // Reads an image from the webcam and associates it with a specific class
  // index.
  const addExample = classId => {
    // Get the intermediate activation of MobileNet 'conv_preds' and pass that
    // to the KNN classifier.
    const activation = net.infer(webcamElement, 'conv_preds');
    console.log(activation);
    // Pass the intermediate activation to the classifier.
    classifier.addExample(activation, classId);
  };

  async function setupWebcam() {
    return new Promise((resolve, reject) => {
      const navigatorAny = navigator;
      navigator.getUserMedia =
        navigator.getUserMedia ||
        navigatorAny.webkitGetUserMedia ||
        navigatorAny.mozGetUserMedia ||
        navigatorAny.msGetUserMedia;
      if (navigator.getUserMedia) {
        navigator.getUserMedia(
          { video: true },
          stream => {
            webcamElement.srcObject = stream;
            webcamElement.addEventListener('loadeddata', () => resolve(), false);
          },
          error => reject(),
        );
      } else {
        reject();
      }
    });
  }
</script>

<style>
  section {
    display: grid;
    grid-template-columns: 0.75fr 1.25fr;
    grid-template-rows: 1.5fr 0.5fr;
    grid-template-areas:  'screen debug' 
                          'actions debug';
    grid-gap: 2em;
    align-items: stretch;
    justify-items: stretch;
  }

  .actions {
    grid-area: actions;
    display: flex;
    align-items: center;
    justify-content: flex-start;
  }

  pre {
    white-space: pre-wrap;
  }
</style>

<Loading loading="{!isModelLoaded}" label="Loading model...">
  <section>
    {#if isMounted}
    <video
      bind:this="{webcamElement}"
      autoplay
      playsinline
      muted
      id="webcam"
      width="224"
      height="224"
    ></video>
    
    <div class="actions">
      {#each classes as cls}
        <button on:click={evt=> addExample(cls)}>Class {cls}</button>
      {/each}
    </div>

    {/if} {#if result}
    <pre>{JSON.stringify(result, null, 2)}</pre>
    {/if}
  </section>
</Loading>
