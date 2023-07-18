<script>
  import * as d3 from "d3";

  import countryShapes from "./country-shapes";
  import scaleCanvas from "./scale-canvas";
  import { tweened } from "./tweened-staggered";

  const move = (x, y) => `transform: translate(${x}px, ${y}px)`;

  export let data = [];

  export let nameAccessor = (d) => d[0];
  export let rAccesor = (d) => d[1];
  export let colorAccessor = (d) => d[2];

  let width = 1200;
  $: isVertical = width < 450;
  $: height = isVertical ? 2 : 0.7;

  const sphere = { type: "Sphere" };
  let canvasElement;
  let blankMap;
</script>
