<script>
  import * as d3 from "d3";
  import data from "../data/data";
  import AxisX from "../components/AxisX.svelte";
  import AxisY from "../components/AxisY.svelte";
  import Tooltip from "../components/Tooltip.svelte";

  let width = 400;
  let height = 400;

  const margin = { top: 20, right: 40, bottom: 20, left: 0 };

  $: xScale = d3
    .scaleLinear()
    .domain([0, 100])
    .range([0, width - margin.left - margin.right]);
  const yScale = d3
    .scaleLinear()
    .domain([0, d3.max(data, (d) => d.hours)])
    .range([height - margin.top - margin.bottom, 0]);

  let hoveredData;
</script>

<main
  class="chart-container"
  bind:clientWidth={width}
  on:mouseleave={() => {
    hoveredData = null;
  }}
>
  <h1>Students who studied longer scored higher on their final exams</h1>
  <svg {width} {height}>
    <AxisX {height} {xScale} {margin} />
    <AxisY {yScale} {width} {margin} />
    <g class="circles" transform="translate({margin.left} {margin.top})">
      {#each data.sort((a, b) => a.grade - b.grade) as student}
        <circle
          cx={xScale(student.grade)}
          cy={yScale(student.hours)}
          r={hoveredData && hoveredData === student ? "20" : "10"}
          opacity={hoveredData ? (hoveredData === student ? "1" : ".3") : "1"}
          fill="pink"
          stroke="black"
          on:mouseover={() => {
            hoveredData = student;
          }}
          on:focus={() => {
            hoveredData = student;
          }}
          tabIndex="0"
        />
      {/each}
    </g>
  </svg>
  {#if hoveredData}
    <Tooltip data={hoveredData} {xScale} {yScale} />
  {/if}
</main>

<style>
  .chart-container {
    max-width: 900px;
    margin: 0 auto;
  }
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
</style>
