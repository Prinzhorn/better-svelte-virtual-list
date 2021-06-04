<script>
  import { onMount, onDestroy } from 'svelte';
  import VirtualList from './VirtualList.svelte';

  let simulateFeed = true;
  let books = [];

  // Insert dummy data.
  // You can also use a store or whatever you need.
  // This easily works with 1 million items but Svelte REPL thinks it's an endless loop.
  for (let i = 0; i < 10_000; i++) {
    books.push({
      id: i,
      title: String(Math.random()),
    });
  }

  let container;
  let scrollTop = 0;
  let containerHeight = 0;

  // Fixed item height in pixels.
  // This could come directly from a store (e.g. if users can configure the height in the UI).
  // Or be derived from a store that holds the base font size (e.g. user can configure the font size in your app).
  // Or calculated using DOM. Whatever your requirements are.
  let itemHeight = 30;

  let frame;
  let timer;

  function poll() {
    if (container.scrollTop !== scrollTop) {
      scrollTop = container.scrollTop;
    }

    frame = requestAnimationFrame(poll);
  }

  onMount(() => {
    frame = requestAnimationFrame(poll);

    // Simulate new data arriving.
    timer = setInterval(() => {
      if (!simulateFeed) {
        return;
      }

      for (let i = 0; i < Math.random() * 10; i++) {
        books.push({
          id: books.length,
          title: String(Math.random()),
        });
      }

      books = books;
    }, 100);
  });

  onDestroy(() => {
    cancelAnimationFrame(poll);
    clearInterval(timer);
  });
</script>

<h1>Books {books.length}</h1>

<div class="books" bind:this={container} bind:clientHeight={containerHeight}>
  <VirtualList items={books} {itemHeight} {containerHeight} {scrollTop} let:item let:dummy let:y>
    <div class="book" class:dummy style="top:{y}px;">
      {#if !dummy}#{item.id} - {item.title}{/if}
    </div>
  </VirtualList>
</div>

<p>
  <button type="button" on:click={() => (books = [])}>Empty</button>
  <label><input type="checkbox" bind:checked={simulateFeed} /> Simulate feed (append new items)</label>
</p>

<style>
  .books {
    overflow: auto;

    /* 2021 inline-block happy fun time  */
    font-size: 0;
    line-height: 0;

    /* This is just for the demo, your scrolling container can have any size  */
    border: 1px solid #000;
    width: 500px;
    height: 500px;
  }

  .book {
    /* You can also use inline-grid and define grid-template-* on the parent for easy virtual grid */
    display: inline-block;

    /* One inline-block per line, no horizontal scrolling  */
    box-sizing: border-box;
    padding: 0 0.5rem;
    width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;

    /* We re-use items when they leave the viewport by adjusting their "top" value */
    /* Depending on your use case you might achieve better results using translate3d + will-change */
    position: relative;

    /* Demo specific row styles  */
    height: 30px;
    line-height: 30px;
    font-size: 15px;
    border-bottom: 1px solid #333;
  }

  .dummy {
    /* The dummy is just an invisible empty item */
    /* But if you want that look you can actually render it like an empty cell */
    border-color: transparent;
  }
</style>
