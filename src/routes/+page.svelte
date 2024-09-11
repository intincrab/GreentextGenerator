<script>
  import { onMount } from 'svelte';
  import html2canvas from 'html2canvas';

  let text = '';
  let fileInput;
  let imagePreview = null;
  let fileSize = '0 KB none';
  let postId = '';

  onMount(() => {
    postId = Math.floor(Math.random() * 100000000).toString();
  });

  function handleUpload(event) {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        imagePreview = e.target.result;
      };
      reader.readAsDataURL(file);
      fileSize = `${(file.size / 1024).toFixed(2)} KB ${file.type.split('/')[1].toUpperCase()}`;
    }
  }

  function handleDownload() {
    html2canvas(document.querySelector('.content')).then(canvas => {
      const link = document.createElement('a');
      link.download = 'post.png';
      link.href = canvas.toDataURL();
      link.click();
    });
  }

  function handleInput(event) {
    const textarea = event.target;
    const lines = textarea.value.split('\n');
    const formattedLines = lines.map(line => 
      line.startsWith('>') ? `<span style="color: #2a8b2a;">${line}</span>` : line
    );
    const formattedText = formattedLines.join('\n') || '<br>'; // Ensure there's always content
    
    const div = textarea.nextElementSibling;
    div.innerHTML = formattedText;
    
    // Sync scroll position
    div.scrollTop = textarea.scrollTop;
  }
</script>

<main>
  <div class="controls">
    <button on:click={() => fileInput.click()}>Upload Image</button>
    <input
      type="file"
      bind:this={fileInput}
      on:change={handleUpload}
      accept="image/*"
      style="display: none;"
    />
    <button on:click={handleDownload}>Download</button>
  </div>
  <div class="content">
    <div class="metadata">
      <span class="green"><strong>Anonymous</strong></span>
      <span class="date">13/7/2024, 7:42:30 pm</span>
      <span class="red">No.{postId}</span>
    </div>
    <div class="body">
      <div class="image-container">
        <div class="image-preview">
          {#if imagePreview}
            <!-- svelte-ignore a11y-img-redundant-alt -->
            <img src={imagePreview} alt="Uploaded image" />
          {:else}
            <div class="preview-text">sample</div>
          {/if}
        </div>
        <div class="file-size">{fileSize}</div>
      </div>
      <div class="text-area">
        <textarea 
          bind:value={text} 
          on:input={handleInput}
          placeholder="Write your greentext story anon."
        ></textarea>
        <div class="formatted-text"></div>
      </div>
    </div>
  </div>
</main>

<style>
  main {
    width: 600px;
    margin: 20px auto;
    font-family: Arial, sans-serif;
  }
  
  .controls {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }
  
  .controls button {
    padding: 8px 16px;
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: white;
    cursor: pointer;
    transition: background-color 0.3s;
  }
  
  .controls button:hover {
    background-color: #e6e6e6;
  }
  
  .content {
    background-color: #F0E0D6;
    padding: 10px;
    border-radius: 5px;
  }
  
  .metadata {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
    font-size: 0.9em;
  }
  
  .green { color: #2a8b2a; }
  .red, .date { color: #810000; }
  
  .body {
    display: flex;
    gap: 10px;
  }
  
  .image-container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .image-preview {
    width: 150px;
    height: 150px;
    background-color: #E2D1C6;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
    border: 1px solid #D9BFB7;
  }
  
  .image-preview img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .preview-text {
    color: #8A0000;
  }
  
  .text-area {
    position: relative;
    width: 100%;
    height: 150px;
  }

  textarea {
    width: 100%;
    height: 100%;
    resize: none;
    border: none;
    background-color: transparent;
    font-family: Arial, sans-serif;
    font-size: 14px;
    color: transparent;
    caret-color: #8A0000;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
    padding: 5px;
  }

  textarea::placeholder {
    color: #8A0000;
    opacity: 0.9; /* This makes the placeholder translucent */
  }

  .formatted-text {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-color: #F0E0D6;
    color: #8A0000;
    font-family: Arial, sans-serif;
    font-size: 14px;
    white-space: pre-wrap;
    word-break: break-word;
    overflow-y: auto;
    pointer-events: none;
    padding: 5px;
  }
  
  .file-size {
    font-size: 0.8em;
    margin-top: 5px;
    text-align: center;
  }
  
</style>