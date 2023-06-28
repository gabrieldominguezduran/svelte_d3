<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";

  let dataset = [];
  let linearScale;

  onMount(async () => {
    dataset = await d3
      .csv("data/nasa_temp.csv")
      .then((data) => {
        return data.map((d) => {
          return {
            year: parseFloat(d.Year) || 0.0,
            avg: parseFloat(d["J-D"]) || 0.0,
          };
        });
      })
      .catch((error) => console.error("Error:", error.message));

    linearScale = d3
      .scaleLinear()
      .domain([d3.min(dataset, (d) => d.avg), d3.max(dataset, (d) => d.avg)])
      .range([0, colors.length - 1]);
  });
  let width = 400;
  let height = 600;
  const xTicks = [1880, 1920, 1960, 2000];
  const margin = { top: 20, right: 40, bottom: 200, left: 0 };

  const colors = [
    "#023858",
    "#045a8d",
    "#0570b0",
    "#3690c0",
    "#74a9cf",
    "#a6bddb",
    "#d0d1e6",
    "#ece7f2",
    "#fff7fb",
    "#fff7ec",
    "#fee8c8",
    "#fdd49e",
    "#fdbb84",
    "#fc8d59",
    "#ef6548",
    "#d7301f",
    "#b30000",
    "#7f0000",
  ];
</script>

<main class="chart-container" bind:clientWidth={width}>
  <h1>Warming Stripes</h1>
  <svg {width} {height}>
    <g>
      {#each dataset as d, i}
        <rect
          x={i * 6}
          y="0"
          width="6"
          height="400"
          fill={colors[Math.round(linearScale(d.avg))]}
          title={`Year: ${d.year} Avg: ${d.avg}`}
        />
      {/each}
    </g>
    <g>
      {#each xTicks as tick}
        <text
          x={tick === 1880
            ? 0
            : tick === 1920
            ? 280
            : tick === 1960
            ? 550
            : 825}
          y={height - margin.bottom}
          dy="6"
          dominant-baseline="hanging"
          fill="white"
        >
          {tick}
        </text>
      {/each}
      <line
        x1={0}
        y1={height - margin.bottom}
        x2={0}
        y2={margin.top}
        stroke="white"
      />
    </g>
  </svg>
</main>

<style>
  .chart-container {
    max-width: 900px;
    margin: 2rem auto;
  }
</style>
