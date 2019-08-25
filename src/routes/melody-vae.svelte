<script>
  import { onMount } from 'svelte';

  // Initialize the model.
  let vaeModel, vaePlayer;
  let temperature = 1.5;
  let isModelReady = false;

  async function playVAE(evt) {
    if (vaePlayer.isPlaying()) {
      vaePlayer.stop();
      return;
    }
    const fromSeq = {
      totalQuantizedSteps: 96,
      quantizationInfo: { stepsPerQuarter: 24 },
      notes: [
        {
          pitch: 'C1',
          velocity: 80,
          quantizedStartStep: 0,
          quantizedEndStep: 45,
          instrument: 0,
        },
        {
          pitch: 'C2',
          velocity: 95,
          quantizedStartStep: 0,
          quantizedEndStep: 12,
          instrument: 1,
        },
      ],
    };
    const toSeq = {
      totalQuantizedSteps: 96,
      quantizationInfo: { stepsPerQuarter: 24 },
      notes: [
        {
          pitch: 'C3',
          velocity: 95,
          quantizedStartStep: 0,
          quantizedEndStep: 12,
          instrument: 0,
        },
        {
          pitch: 'C4',
          velocity: 95,
          quantizedStartStep: 0,
          quantizedEndStep: 24,
          instrument: 1,
        },
      ],
    };
    let patterns;
    vaeModel
      .sample(1, temperature)
      .then(([s1, s2]) => vae.interpolate([a1, s2, 8]))
      .then(res => (patterns = res));
    // .then((sample) => vaePlayer.start(sample[0]));
    // let result = await vaeModel.interpolate([fromSeq, toSeq], 5, temperature, ['CM'])
  }

  onMount(async () => {
    vaeModel = new mm.MusicVAE(
      'https://storage.googleapis.com/magentadata/js/checkpoints/vaeModel/mel_16bar_small_q2',
    );
    await vaeModel.initialize();
    isModelReady = true;
    // Create a player to play the sampled sequence.
    vaePlayer = new mm.Player();
  });
</script>

<svelte:head>
  <title>Home</title>

  <script src="https://cdn.jsdelivr.net/npm/@magenta/music@1.4.2">

  </script>
</svelte:head>
<button on:click={playVAE} disabled={!isModelReady}>Play</button>

<input bind:value={temperature} type="number" disabled={!isModelReady} />
