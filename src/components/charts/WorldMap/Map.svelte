<script>
  import * as d3 from "d3";

  import countryShapes from "./country-shapes";
  import scaleCanvas from "./scale-canvas";
  import { tweened } from "./tweened-staggered";

  // utility function for translating elements
  const move = (x, y) => `transform: translate(${x}px, ${y}px)`;

  export let data = [];

  // accessor functions to quickly pivot between data structures
  export let nameAccessor = (d) => d[0];
  export let rAccessor = (d) => d[1];
  export let colorAccessor = (d) => d[2];

  let width = 1200;
  $: isVertical = width < 450;
  $: height = width * (isVertical ? 2 : 0.7);

  const sphere = { type: "Sphere" };
  let canvasElement;
  let blankMap;

  // create projections - vertical orientations have two globes
  $: projections = isVertical
    ? [
        d3.geoOrthographic().fitSize([width, width], sphere).rotate([-75, -20]),
        d3
          .geoOrthographic()
          .fitSize([width, width], sphere)
          .translate([width / 2, height * 0.7])
          .rotate([70, -20]),
      ]
    : [d3.geoOrthographic().fitSize([width, height], sphere).rotate([-6, 0])];

  //scales
  $: rScale = d3
    .scaleSqrt()
    .domain([0, d3.max(data, rAccessor)])
    .range([0, width * height * 0.000036])
    .clamp(true);

  $: colorScale = d3
    .scaleLinear()
    .domain([0, d3.extent(data, colorAccessor)])
    .range(["#274B55", "#f8f8f8"])
    .interpolate(d3.interpolateHcl)
    .clamp(true);

  // draw blank map
  const drawCanvas = () => {
    if (!canvasElement) return;
    const ctx = canvasElement.getContext("2d");
    scaleCanvas(canvasElement, ctx, width, height);

    const drawMap = (projection) => {
      const path = d3.geoPath(projection, ctx);
      const drawPath = (shape) => {
        ctx.beginPath();
        path(shape);
      };
      drawPath(sphere);

      const fill = (color) => {
        ctx.fillStyle = color;
        ctx.fill();
      };
      const stroke = (color) => {
        ctx.strokeStyle = color;
        ctx.stroke();
      };
      drawPath(sphere);
      fill("#fff");
      stroke("#bbb");
      drawPath(d3.geoGraticule10());
      stroke("#eee");

      countryShapes.forEach((shape) => {
        drawPath(shape);
        fill("#f8f8f8");
        stroke("#ccc");
      });

      drawPath(sphere);
      stroke("#ccc");
    };
    projections.forEach(drawMap);

    blankMap = ctx.getImageData(0, 0, width * 2, height * 2);
  };

  $: width, projections, drawCanvas();
</script>

<figure class="canvas-wrapper" bind:clientWidth={width}>
  <canvas
    style={`width: ${width}px; height: ${height}px`}
    bind:this={canvasElement}
  />
</figure>

<style>
  .canvas-wrapper {
    position: relative;
    width: 100%;
    margin: 0;
  }
</style>
