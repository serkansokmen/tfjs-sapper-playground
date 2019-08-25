<script>
  import { onMount, createEventDispatcher } from 'svelte';
  import Dropzone from 'svelte-dropzone';

  let imgSrc;

  const dispatch = createEventDispatcher();

  const options = {
    thumbnailWidth: 250,
    thumbnailHeight: 250,
    clickable: true,
    acceptedFiles: '*',
    maxFilesize: 256,
    autoDiscover: false,
  };

  const dropzoneEvents = {
    addedfile: file => {
      let img = new Image();
      let imgWidth = 0;
      let imgHeight = 0;
      const maxWidth = 640;
      const maxHeight = 640;
      img.src = URL.createObjectURL(file);
      img.onload = function() {
        imgWidth = this.width;
        imgHeight = this.height;
        imgSrc = this.src;
        dispatch('added', img);
      };
    },
    drop: event => dispatch('drop', event.target),
    init: () => dispatch('ready'),
  };

  function handleReset(evt) {
    imgSrc = null;
    dispatch('reset');
  }
</script>

<style>
  img {
    max-width: 100%;
  }
  img.hidden {
    visibility: hidden;
    opacity: 0;
  }
</style>

{#if imgSrc}
<img src="{imgSrc}" alt="Source" class="{imgSrc ? '' : 'hidden'}" />
<button on:click="{handleReset}">Remove Image</button>
{:else}
<Dropzone id="dropzone" {dropzoneEvents} {options}>
  <p>Drop files here to upload</p>
</Dropzone>
{/if}
