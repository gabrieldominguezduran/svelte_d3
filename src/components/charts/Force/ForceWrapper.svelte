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

  const numberOfDots = 100;
  let dots = new Array(numberOfDots).fill(0).map((_) => ({}));

  const onClick = (e) => {
    if (!element) return;

    const bounds = element.getBoundingClientRect();
    const x = e.clientX - bounds.left;
    const y = e.clientY - bounds.top;
    centerPosition = [x, y];
  };
</script>

<main class="chart-container">
  <h1>Force</h1>
  <div class="note">Click to update</div>
  <div class="controls">
    <label>
      <input type="checkbox" bind:checked={useForceCollide} />
      Collide?
    </label>
    <label>
      <input type="checkbox" bind:checked={useForceRadial} />
      Radial?
    </label>
  </div>

  <div on:click={onClick} bind:this={element}>
    <Force {dots} {forces} />
  </div>
</main>

<style>
  .controls {
    display: flex;
    justify-content: flex-end;
    margin-top: -1rem;
    margin-bottom: 2rem;
  }
  label + label {
    margin-left: 1em;
  }
  .note {
    font-style: italic;
    color: var(--text-light);
  }
</style>
