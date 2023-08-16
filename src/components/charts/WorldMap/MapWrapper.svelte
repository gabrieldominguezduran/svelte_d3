<script>
  import countryShapes from "./country-shapes.js";
  import Map from "./Map.svelte";

  const createData = () => {
    return countryShapes.map((d) => [
      d.properties.geounit,
      Math.random() < 0.13 ? Math.random() : 0,
      Math.random(),
    ]);
  };
  let data = createData();
</script>

<main class="chart-container">
  <h1>Map</h1>
  <div class="note">Click to update</div>
  <div class="wrapper">
    <Map
      {data}
      nameAccessor={(d) => d[0]}
      rAccessor={(d) => d[1]}
      colorAccessor={(d) => d[2]}
    />
  </div>
</main>

<style>
  .wrapper {
    background: #f8f8f8;
    margin: 0;
    padding: 1rem;
  }
  .note {
    font-style: italic;
    color: var(--text-light);
  }
</style>
