<script>
  import Map from "./Map.svelte";
</script>

<main class="chart-container scatter">
  <h1>Map</h1>
  <div class="note">CLick to update</div>
  <Map />
</main>

<style>
  .note {
    font-style: italic;
    color: var(--text-light);
  }
</style>
