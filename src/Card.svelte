<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  export let content;


</script>

<style>
  .card {
    position: relative;
    background: white;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0 , .15);
    padding: 15px 35px 15px 20px;
    margin-bottom: 10px;
  }

  .next {
    position: absolute;
    color: white;
    height: 100%;
    top: 0;
    right: 0;
    background: hsla(230, 100%, 50%, 0.7);
    width: 25px;
    cursor: pointer;
    user-select: none;

    display: flex;
    justify-content: center;
    align-items: center;

    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;

    transition: width .2s, right .2s;
  }

  .next:hover:not(:active) {
    width: 30px;
    right: -5px;
  }

  p[contenteditable] {
    outline: 0;
  }
</style>

<div class="card">
  
  <p contenteditable bind:innerHTML={content}></p>
  <div class="next" on:click={() => dispatch('next')}>▶</div>
</div>