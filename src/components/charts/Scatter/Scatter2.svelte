<script>
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let width = 950;
  let height = 500;

  $: mainWidth = width - margin.left - margin.right;
  $: mainHeight = height - margin.top - margin.bottom;

  const margin = { top: 20, right: 40, bottom: 20, left: 0 };
  let dataset = [];
  let xScale;
  let yScale;
  let xTicks = [];
  let yTicks = [];
  let hoveredData;

  onMount(async () => {
    dataset = await d3.csv("data/scatter.csv").then((data) => {
      return data.map((d) => {
        return {
          price: d.price,
          carat: d.carat,
        };
      });
    });

    xScale = d3
      .scaleLinear()
      .domain([0, d3.max(dataset, (d) => d.price)])
      .range([0, mainWidth]);

    yScale = d3
      .scaleLinear()
      .domain([0, d3.max(dataset, (d) => d.carat)])
      .range([mainHeight, 0]);

    xTicks = d3.range(
      0,
      d3.max(dataset, (d) => d.price),
      2000
    );

    yTicks = d3.range(
      0,
      d3.max(dataset, (d) => d.carat),
      0.5
    );
  });
</script>

<main
  class="chart-container scatter"
  bind:clientWidth={width}
  on:mouseleave={() => {
    hoveredData = null;
  }}
>
  <h1>Diamond scatter chart</h1>
  {#if dataset.length === 0}
    <p>Loading...</p>
  {:else}
    <svg {width} {height}>
      <g>
        {#each xTicks as tick}
          <text
            x={xScale(tick - 100)}
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
      <g transform="translate({margin.left} {margin.top - 30})">
        {#each yTicks as tick}
          <text x={0} y={yScale(tick)} dy="-6" dx="3" fill="white">
            {tick}
          </text>
        {/each}
        <line
          x1={0}
          y1={height - margin.bottom}
          x2={width - margin.right}
          y2={height - margin.bottom}
          stroke="white"
        />
      </g>
      <g class="circles" transform="translate({margin.left} {margin.top - 30})">
        {#each dataset as d}
          <circle
            cx={xScale(d.price)}
            cy={yScale(d.carat)}
            r={hoveredData && hoveredData === d ? "6" : "2"}
            fill="#33FEFF"
            opacity="0.5"
            on:mouseover={() => {
              hoveredData = d;
            }}
            on:focus={() => {
              hoveredData = d;
            }}
            on:mouseout={() => {
              hoveredData = null;
            }}
            on:blur={() => {
              hoveredData = null;
            }}
          />
        {/each}
      </g>
    </svg>
  {/if}
  {#if hoveredData}
    <div
      class="tooltip"
      style="position: absolute; top: {yScale(
        hoveredData.carat
      )}px; left: {xScale(hoveredData.price)}px"
    >
      <h4>{hoveredData.carat} ct</h4>
      <p>${hoveredData.price}</p>
    </div>
  {/if}
</main>

<style>
  circle {
    transition: r 300ms ease, opacity 300ms ease;
    cursor: pointer;
  }

  circle:focus {
    outline: none;
  }

  h1 {
    font-size: 1.5rem;
    font-weight: 600;
    margin: 1rem auto;
  }

  .tooltip {
    padding: 0.3rem;
    background: rgba(192, 192, 188, 0.742);
    border: 1px solid rgba(0, 0, 0, 0.5);
    border-radius: 5px;
    pointer-events: none;
    transition: top 300ms ease, left 300ms ease;
  }

  h4 {
    font-weight: 700;
    margin: 0;
    margin-bottom: 0.5rem;
  }

  p {
    margin: 0;
  }
</style>
