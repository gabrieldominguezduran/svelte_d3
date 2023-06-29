<script>
  import * as d3 from "d3";

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

  let yTicks = [0, 20, 40, 60];
  let xTicks = [0, 25, 50, 75, 100];
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
    <g>
      {#each xTicks as tick}
        <text
          x={xScale(tick)}
          y={height - margin.bottom}
          dy="6"
          dominant-baseline="hanging"
          fill="white"
        >
          {tick}%
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
    <g transform="translate({margin.left} {margin.top})">
      {#each yTicks as tick}
        <text x={0} y={yScale(tick)} dy="-6" dx="3" fill="white">
          {tick}
          {tick === 60 ? " hours studied" : ""}
        </text>
        <line
          x1={0}
          y1={yScale(tick)}
          x2={width}
          y2={yScale(tick)}
          stroke={tick === 0 ? "white" : "lightgrey"}
        />
      {/each}
    </g>
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
    <div
      class="tooltip"
      style="position: absolute; top: {yScale(
        hoveredData.hours
      )}px; left: {xScale(hoveredData.grade)}px"
    >
      <h4>{hoveredData.name}</h4>
      <p>{hoveredData.hours} hours studied</p>
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
