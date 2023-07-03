<script>
  import { onMount } from "svelte";
  import { timeFormat } from "d3-time-format";
  import * as d3 from "d3";

  let dataset = [];
  let line;
  let xScale;
  let yScale;
  let xTickValues = [];
  let yTicks = [];

  let width = 950;
  let height = 400;

  const margin = { top: 20, right: 40, bottom: 20, left: 0 };

  onMount(async () => {
    dataset = await d3.csv("data/line.csv").then((data) => {
      return data.map((d) => {
        return {
          date: d.Date ? d3.timeParse("%Y-%m-%d")(d.Date) : null,
          value: d.Open,
        };
      });
    });
    xScale = d3
      .scaleTime()
      .domain(d3.extent(dataset, (d) => d.date))
      .range([0, width]);

    yScale = d3
      .scaleLinear()
      .domain([0, d3.max(dataset, (d) => d.value)])
      .range([height, 0]);

    line = d3
      .line()
      .x((d) => xScale(d.date))
      .y((d) => yScale(d.value));

    xTickValues = d3.timeMonths(
      d3.min(dataset, (d) => d.date),
      d3.max(dataset, (d) => d.date)
    );

    yTicks = d3.range(
      0,
      d3.max(dataset, (d) => d.value),
      20
    );
  });
</script>

<main class="chart-container" bind:clientWidth={width}>
  <h1>Line Chart</h1>
  <svg {width} {height}>
    <g>
      {#each xTickValues as tick}
        <text
          x={xScale(tick) + 10}
          y={height - margin.bottom}
          dy="6"
          dominant-baseline="hanging"
          fill="white"
        >
          {timeFormat("%b %y")(tick)}
        </text>
      {/each}
      <g>
        <line
          x1={0}
          y1={height - margin.bottom}
          x2={width - margin.right}
          y2={height - margin.bottom}
          stroke="white"
        />
        {#each yTicks as tick}
          <text x={margin.left} y={yScale(tick)} dy="-6" dx="3" fill="white">
            {tick.toFixed(0)}
          </text>
        {/each}
        <line
          x1={margin.left}
          y1={margin.top}
          x2={margin.left}
          y2={height - margin.bottom}
          stroke="white"
        />
      </g>
      <g>
        {#if dataset.length > 0}
          <path
            d={line(dataset)}
            fill="none"
            stroke="yellow"
            stroke-width="2"
          />
        {/if}
      </g>
    </g></svg
  >
</main>
