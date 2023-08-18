<script>
  import * as d3 from "d3";

  import Force from "./Force.svelte";

  let element;

  let centerPosition = [200, 200];
  let useForceCollide = true;
  let useForceRadial = true;
  $: activeForceX = d3.forceX().x(centerPosition[0]);
  $: activeForceY = d3.forceY().y(centerPosition[1]);
  $: activeForceCollide = d3.forceCollide().radius(10).iterations(3);
  $: activeForceRadial = d3
    .forceRadial()
    .radius(150)
    .x(centerPosition[0])
    .y(centerPosition[1]);

  $: forces = [
    ["x", activeForceX],
    ["y", activeForceY],
    useForceCollide && ["collide", activeForceCollide],
    useForceRadial && ["radial", activeForceRadial],
  ].filter((d) => d);
</script>

<main class="chart-container">
  <h1>Force</h1>
  <div class="note">Click to update</div>
  <div class="wrapper" />
</main>

<style>
  .note {
    font-style: italic;
    color: var(--text-light);
  }
</style>
