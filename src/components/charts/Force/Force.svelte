<script>
  import * as d3 from "d3";

  const move = (x, y) => `transform: translate(${x}px, ${y}px)`;

  export let dots = [];
  export let forces = [];

  let usedForces = [];
  let renderDots = [];

  let width = 1200;
  $: height = width;

  $: simulation = d3
    .forceSimulation()
    .nodes(dots)
    .on("tick", () => {
      renderDots = [...dots];
    });
</script>
