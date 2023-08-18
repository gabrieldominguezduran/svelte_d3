<script>
  import * as d3 from "d3";

  const move = (x, y) => `transform: translate(${x}px, ${y}px)`;

  export let dots = [];
  export let forces = [];

  let usedForcesNames = [];
  let renderedDots = [];

  let width = 1200;
  $: height = width / 2;

  $: simulation = d3
    .forceSimulation()
    .nodes(dots)
    .on("tick", () => {
      renderedDots = [...dots];
    });

  $: {
    forces.forEach(([name, force]) => {
      simulation.force(name, force);
    });

    const newForcesNames = forces.map(([name]) => name);
    let oldForcesNames = usedForcesNames.filter(
      (force) => !newForcesNames.includes(force)
    );

    oldForcesNames.forEach((name) => {
      simulation.force(name, null);
    });

    usedForcesNames = newForcesNames;

    simulation.alpha(1);
    simulation.restart();
  }
</script>

<figure class="c" bind:clientWidth={width}>
  <svg {width} {height}>
    {#each renderedDots as { x, y }, i}
      <circle r="6" style={move(x, y)} />
    {/each}
  </svg>
</figure>

<style>
  figure {
    margin: 0;
  }

  svg {
    overflow: visible;
  }

  circle {
    fill: #fcfcfc;
  }
</style>
