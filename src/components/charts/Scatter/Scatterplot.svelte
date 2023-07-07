<script>
  import { spring } from "svelte/motion";
  import { cubicOut } from "svelte/easing";
  import * as d3 from "d3";

  const move = (x, y) => `transform: translate(${x}px, ${y}px)`;

  export let data = [];

  export let xAccesor = (d) => d[0];
  export let yAccesor = (d) => d[1];
  export let rAccesor = (d) => d[2];

  export let margin = { top: 20, right: 20, bottom: 20, left: 20 };

  let width = 1200;
  $: height = width;
  $: mainWidth = width - margin.left - margin.right;
  $: mainHeight = height - margin.top - margin.bottom;

  let dots = spring(
    data.map((d, i) => ({
      x: 0,
      y: 0,
      r: 0,
    })),
    {
      stiffness: 0.1,
      damping: 0.9,
    }
  );

  $: xScale = d3
    .scaleLinear()
    .domain(d3.extent(data, xAccesor))
    .range([0, mainWidth]);
  $: yScale = d3
    .scaleLinear()
    .domain(d3.extent(data, yAccesor))
    .range([mainHeight, 0]);
  $: rScale = d3.scaleLinear().domain(d3.extent(data, rAccesor)).range([0, 20]);
  const colorScale = d3
    .scaleLinear()
    .domain([0, 20])
    .range(["tomato", "cornflowerblue"])
    .interpolate(d3.interpolateHcl);

  const updatedata = () => {
    const newDots = data.map((d, i) => ({
      x: xScale(xAccesor(d)),
      y: yScale(yAccesor(d)),
      r: rScale(rAccesor(d)),
    }));
    dots.set(newDots);
  };

  $: data, mainWidth, updatedata();
</script>

<figure class="c" bind:clientWidth={width}>
  <svg {width} {height}>
    <g style={move(margin.top, margin.left)}>
      {#each dots as { x, y, r }}
        <circle style={move(x, y)} r={Math.max(0, r)} fill={colorScale(r)} />
      {/each}
    </g>
  </svg>
</figure>
