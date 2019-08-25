<script>
  let output;
  let img;

  let vector = randomVector(140, 0, 0.5);
  let direction;

  function normal(mu = 0, sigma = 1, nSamples = 10) {
    let runTotal = 0;
    for (let i = 0; i < nSamples; i++) {
      runTotal += Math.random();
    }
    return (sigma * (runTotal - nSamples / 2)) / (nSamples / 2) + mu;
  }

  function randomVector(length, mean, sigma) {
    const ret = [];
    for (var i = 0; i < length; i++) {
      ret.push(normal(mean, sigma));
    }
    return ret;
  }

  function addVectors(vecs) {
    const ret = [];
    for (let i = 0; i < vecs[0].length; i++) {
      let val = 0;
      for (let j = 0; j < vecs.length; j++) {
        val += vecs[j][i];
      }
      ret.push(val);
    }
    return ret;
  }

  // noCanvas();
  changeDirection();
  resample();

  $: {
    if (img) {
      img.position(0, 0);
      img.size(512, 512);
    }
  }

  function changeDirection() {
    direction = randomVector(140, 0, 0.2);
  }

  function resample() {
    vector = addVectors([vector, direction]);

    const inputs = {
      z: vector,
      // truncation: 0.7,
      category: 'stingray',
    };

    fetch('http://localhost:8001/query', {
      method: 'POST',
      headers: {
        Accept: 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(inputs),
    })
      .then(response => response.json())
      .then(resp => {
        // if (img) {
        //   img.remove();
        // }
        debugger;
        const reader = new FileReader();
        reader.onload = () => {
          img = reader.result;
        };
        console.log(resp.output);
        // debugger
        // reader.readAsDataURL(resp.output);
        // img = createImg(resp.output);
        resample();
        if (Math.random() < 0.25) {
          changeDirection();
        }
      });
  }
</script>

{#if img}
  <img {...img} />
{/if}
