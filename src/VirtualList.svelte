<script>
  // When using a `position: sticky` header or footer you need to pass the height here.
  export let footerHeight = 0;
  export let headerHeight = 0;
  export let items;
  export let containerHeight;
  export let itemHeight;
  export let scrollTop;

  const dummySymbol = Symbol('dummy item');

  function sliceArray(arr, start, end) {
    arr = arr.slice(start, end);

    let expectedLength = end - start;

    // If we don't have enough items we'll fill it up with dummy entries.
    // This makes everything a lot easier, consistent and less edge-casey.
    while (arr.length < expectedLength) {
      arr.push(dummySymbol);
    }

    return arr;
  }

  function shiftArray(arr, count) {
    // Could probably be optimized, but it runs on just dozens of items so relax.
    for (let i = 0; i < count; i++) {
      arr.unshift(arr.pop());
    }
    return arr;
  }

  $: lostHeight = headerHeight + footerHeight;
  $: spacerHeight = Math.max(containerHeight - lostHeight, items.length * itemHeight);
  $: numItems = Math.ceil((containerHeight + lostHeight) / itemHeight) + 1;
  $: startIndex = Math.floor(scrollTop / itemHeight);
  $: endIndex = startIndex + numItems;
  $: numOverlap = startIndex % numItems;
  $: blockHeight = numItems * itemHeight;
  $: globalOffset = blockHeight * Math.floor(scrollTop / blockHeight);
  $: slice = shiftArray(sliceArray(items, startIndex, endIndex), numOverlap);
</script>

<div class="spacer" style="height: {spacerHeight}px;" tabindex="-1" on:keydown on:wheel>
  {#each slice as item, index}
    <slot
      item={item === dummySymbol ? undefined : item}
      dummy={item === dummySymbol}
      y={globalOffset + (index < numOverlap ? blockHeight : 0)}
    />
  {/each}
</div>

<style>
  .spacer {
    width: 100%;

    /* Prevent the translated items from bleeding through, causing more scrolling */
    overflow: hidden;

    /* 2021 inline-block happy fun time  */
    font-size: 0;
    line-height: 0;
  }
</style>
