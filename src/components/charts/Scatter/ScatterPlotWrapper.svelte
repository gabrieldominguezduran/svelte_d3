<script>
  import Scatterplot from "./Scatterplot.svelte";

  let interation = 3;

  const createData = () => {
    interation = interation + 1;

    return new Array(300)
      .fill(0)
      .map((d, i) => [
        Math.random() * 100,
        Math.random() * 100,
        Math.random() * 100,
      ]);
  };

  let data = createData();
</script>

<main class="chart-container scatter">
  <h1>Dots</h1>
  <div class="note">Click to update</div>
  <Scatterplot {data} />
</main>
<svelte:window
  on:click={() => (data = createData())}
  on:touchend={() => (data = createData())}
/>

<style>
  .note {
    font-style: italic;
    color: var(--text-light);
  }
</style>
