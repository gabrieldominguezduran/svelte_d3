<script>
  export let height;
  export let xScale;
  export let margin;
  let xTicks = [0, 25, 50, 75, 100];
</script>

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
