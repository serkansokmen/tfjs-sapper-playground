<script>
  import { createEventDispatcher } from 'svelte';

  let files;
  let result;

  const dispatch = createEventDispatcher();
  const reader = new FileReader();
  reader.onload = () => {
    result = reader.result;

    setTimeout(() => {
      const img = document.getElementById('result');
      // console.log(img)
      dispatch('read', img);
    }, 250);
  };

  function handleImages(evt) {
    const arr = Array.from(evt.target.files);
    if (arr.length) {
      reader.readAsDataURL(arr[0]);
    }
  }
</script>

<style>
  .wrap {
    max-width: 400px;
    max-height: 400px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>

<div>
  <input type="file" accept="image/*" on:change={handleImages} />
</div>

{#if result}
  <div class="wrap">
    <img id="result" src={result} alt="result" width="400" />
  </div>
{/if}
