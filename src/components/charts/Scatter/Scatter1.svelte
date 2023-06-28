<script>
  import * as d3 from "d3";
  import AxisX from "./AxisX.svelte";
  import AxisY from "./AxisY.svelte";
  import Tooltip from "./Tooltip.svelte";

  const data = [
    { name: "Antonio", hours: 44, grade: 50 },
    { name: "Sai", hours: 60, grade: 99 },
    { name: "Yohan", hours: 23, grade: 50 },
    { name: "Krishna", hours: 15, grade: 34 },
    { name: "Jennifer", hours: 10, grade: 20 },
    { name: "Cedric", hours: 25, grade: 65 },
    { name: "Jaylen", hours: 46, grade: 35 },
    { name: "Ethan", hours: 30, grade: 30 },
    { name: "Roy", hours: 8, grade: 10 },
    { name: "Denizhan", hours: 35, grade: 79 },
    { name: "J", hours: 52, grade: 65 },
    { name: "Greyson", hours: 39, grade: 50 },
    { name: "Marshall", hours: 16, grade: 30 },
    { name: "Jonny", hours: 30, grade: 53 },
    { name: "McKenna", hours: 40, grade: 56 },
    { name: "Drake", hours: 45, grade: 75 },
    { name: "Justin", hours: 14, grade: 49 },
    { name: "Joe", hours: 23, grade: 59 },
  ];

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
      {#each data.sort((a, b) => a.grade - b.grade) as student, i}
        <circle
          cx={xScale(student.grade)}
          cy={yScale(student.hours)}
          r={hoveredData && hoveredData === student ? "20" : "10"}
          opacity={hoveredData ? (hoveredData === student ? "1" : ".3") : "1"}
          fill="yellow"
          stroke="white"
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
