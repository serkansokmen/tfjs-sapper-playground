<script>
  import { onMount } from 'svelte';
  import Loading from '../components/Loading.svelte';

  let loopBeat, baseSynth, cymbalSynth, amSynth, samplerSynth;
  let counter = 0;
  let rnn,
    isModelReady = false;
  let elapsedTime

  onMount(async () => {
    await setupModel();
    isModelReady = true;

    setupMusic();

    // loopBeat.start(0);
    Tone.Transport.start(0).bpm.value = 158;
    elapsedTime = 0
  });

  async function setupModel() {
    // load the model
    const improvCheckpoint =
      'https://storage.googleapis.com/magentadata/js/checkpoints/music_rnn/chord_pitches_improv';
    rnn = new mm.MusicRNN(improvCheckpoint);
    return await rnn.initialize();
  }

  function setupMusic() {
    samplerSynth = new Tone.Sampler({
      C1: 'themes/pack_2/sounds/C1.wav',
      C2: 'themes/pack_2/sounds/C2.wav',
      C3: 'themes/pack_2/sounds/C3.wav',
      C4: 'themes/pack_2/sounds/C4.wav',
    }).toMaster();
    
    amSynth = new Tone.AMSynth().toMaster({
      harmonicity: 1.04, // 3/1
      detune: 0,
      oscillator: {
        type: 'sine',
      },
      envelope: {
        attack: 0.01,
        decay: 0.01,
        sustain: 1,
        release: 0.5,
      },
      modulation: {
        type: 'square',
      },
      modulationEnvelope: {
        attack: 0.005,
        decay: 0,
        sustain: 1,
        release: 0.5,
      },
    });
    baseSynth = new Tone.MembraneSynth().toMaster();
    cymbalSynth = new Tone.MetalSynth({
      frequency: 120,
      envelope: {
        attack: 0.001,
        decay: 0.1,
        release: 0.01,
      },
      harmonicity: 3.1,
      modulationIndex: 16,
      resonance: 8000,
      octaves: 0.5,
    }).toMaster();
    loopBeat = new Tone.Loop(handleSong, '16n');
  }

  function handleSong(time) {
    const { position } = Tone.Transport;

    if (counter % 4 === 0) {
      baseSynth.triggerAttackRelease('F#1', '8n', time, 1);
    }
    if (counter % 4 !== 1) {
      if (counter === 3 || counter === 12) {
        cymbalSynth.envelope.decay = 0.5;
      } else {
        cymbalSynth.envelope.decay = 0.01;
      }
      cymbalSynth.triggerAttackRelease('32n', time, 0.3);
    }

    if (counter === 0) {
      amSynth.triggerAttackRelease('A1', '16n', time, 0.8);
    }

    if (counter === 10) {
      amSynth.triggerAttackRelease('Bb1', '16n', time, 0.8);
    }

    counter = (counter + 1) % 16;
  }

  function handleStop(evt) {
    loopBeat.stop();
    elapsedTime = Tone.Transport.seconds;
    counter = 0;
    Tone.Transport.stop().start()
  }
</script>

<svelte:head>
  <title>Melody</title>
  <script src="https://unpkg.com/tone">

  </script>
  <script src="https://cdn.jsdelivr.net/npm/@magenta/music@1.4.2">

  </script>
</svelte:head>
<Loading loading={!isModelReady}>
  <button on:click={evt => loopBeat.start(0)}>Start</button>
  <button on:click={handleStop}>Stop</button>
</Loading>
