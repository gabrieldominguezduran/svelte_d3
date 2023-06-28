<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";

  let dataset = [];
  let linearScale;
  let hoveredIndex = -1;

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
  let width = 500;
  let height = 600;
  const xTicks = [1880, 1930, 1975, 2022];
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

  function handleMouseOver(index) {
    hoveredIndex = index;
  }

  function handleMouseOut() {
    hoveredIndex = -1;
  }
</script>

<main class="chart-container" bind:clientWidth={width}>
  <h1>Warming Stripes</h1>
  <svg {width} {height}>
    <g>
      {#each dataset as d, i}
        <g
          on:mouseover={() => handleMouseOver(i)}
          on:focus={() => {
            handleMouseOver(i);
          }}
          on:mouseout={handleMouseOut}
          on:blur={() => {
            handleMouseOut;
          }}
        >
          <title>{`Year: ${d.year} Avg: ${d.avg}`}</title>
          <rect
            x={i * 6}
            y="0"
            width="6"
            height="400"
            fill={colors[Math.round(linearScale(d.avg))]}
            title={`Year: ${d.year} Avg: ${d.avg}`}
            class:highlighted={hoveredIndex === i}
          />
        </g>
      {/each}
    </g>
    <g>
      {#each xTicks as tick}
        <text
          x={tick === 1880
            ? 0
            : tick === 1930
            ? 280
            : tick === 1975
            ? 550
            : 820}
          y={height - margin.bottom}
          dy="6"
          dominant-baseline="hanging"
          fill="white"
        >
          {tick === 1880 ? `Year: ${tick}` : tick}
        </text>
      {/each}
    </g>
  </svg>
</main>

<style>
  .chart-container {
    max-width: 900px;
    margin: 2rem auto;
  }

  .highlighted {
    cursor: pointer;
    stroke: black;
    stroke-width: 3;
  }
</style>
