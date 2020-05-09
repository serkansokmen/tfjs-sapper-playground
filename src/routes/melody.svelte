<script>
  import { onMount } from "svelte";
  import Loading from '../components/Loading.svelte';

  let rnn;
  let isModelReady = false;
  let loopBeat, baseSynth, cymbalSynth, amSynth;
  let counter = 0;

  onMount(async () => {
    const improvCheckpoint =
      'https://storage.googleapis.com/magentadata/js/checkpoints/music_rnn/chord_pitches_improv';
    rnn = new mm.MusicRNN(improvCheckpoint);
    await rnn.initialize();

    isModelReady = true;

    loopBeat = new Tone.Loop(handleSong, '16n');

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

    Tone.Transport.start(0);
  });

  function handleSong(time) {
    const { position } = Tone.Transport;
    // if (counter % 4 === 0) {
    //   baseSynth.triggerAttackRelease('F#1', '8n', time, 1);
    // }
    // if (counter % 4 !== 1) {
    //   if (counter === 3 || counter === 12) {
    //     cymbalSynth.envelope.decay = 0.5;
    //   } else {
    //     cymbalSynth.envelope.decay = 0.01;
    //   }
    //   cymbalSynth.triggerAttackRelease('32n', time, 0.3);
    // }

    // if (counter === 0) {
    //   amSynth.triggerAttackRelease('A1', '16n', time, 0.8);
    // }

    // if (counter === 10) {
    //   amSynth.triggerAttackRelease('Bb1', '16n', time, 0.8);
    // }
    counter = (counter + 1) % 16;
  }

  async function generateMelody(evt) {
    const times = 16;
    const temperature = 1.0;
    const cordStructure = ['CM'];
    const seedSeq = {
      totalQuantizedSteps: 16,
      quantizationInfo: { stepsPerQuarter: 1 },
      notes: [
        { pitch: 'A1', quantizedStartStep: 0, quantizedEndStep: 1 },
        { pitch: 'Bb1', quantizedStartStep: 1, quantizedEndStep: 2 },
        { pitch: 'C3', quantizedStartStep: 2, quantizedEndStep: 3 },
        { pitch: 'B3', quantizedStartStep: 3, quantizedEndStep: 4 },
      ],
    };
    let resultSeq = await rnn.continueSequence(seedSeq, times, temperature, cordStructure);
    let allNotes = seedSeq.notes.concat(resultSeq.notes);

    Tone.Transport.scheduleRepeat(time => {
      if (counter >= 16) counter = 0;
      let notes = allNotes.filter(n => n.quantizedStartStep === counter);

      for (let note of notes) {
        console.log(note, time, counter);
        amSynth.triggerAttack(note, time, 0.8);
      }
      counter++;
    }, '16n');

    Tone.Transport.stop().start();
  }

  function handleStop(evt) {
    loopBeat.stop();
    console.log(`Stopped at ${Tone.Transport.seconds.toFixed(3)}`);
    counter = 0;
    Tone.Transport.cancel()
      .stop()
      .start();
  }
</script>

<svelte:head>
  <title>Melody</title>

  <script src="https://cdn.jsdelivr.net/npm/@magenta/music@1.4.2"> </script>
  <script src="https://unpkg.com/tone"> </script>
</svelte:head>

<Loading loading="{!isModelReady}">
  <button type="button" on:click="{generateMelody}">Play</button>
  <button type="button" on:click="{handleStop}">Stop</button>
</Loading>
