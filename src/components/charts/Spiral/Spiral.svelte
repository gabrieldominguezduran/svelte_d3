<script>
  import * as d3 from "d3";
  import { fade } from "svelte/transition";

  export let data = [];
  export let metricAccessor = (d) => d["x"];
  export let timeAccessor = (d) => d["date"];

  const progressInYearAccessor = (d) => {
    const date = timeAccessor(d);
    console.log("date", date, d3.timeDay.count(d3.timeYear.floor(date), date));

    return d3.timeDay.count(d3.timeYear.floor(date), date);
  };

  const formatNumber = d3.format(",.1s");
  const formatDate = d3.timeFormat("%b %-d, %Y");

  let width = 100;
  $: height = width;

  $: metricDomain = [0, 500];
  $: timeDomain = d3.extent(data, timeAccessor);

  $: colorScale = d3
    .scaleLinear()
    .domain([
      0,
      metricDomain[1] / 3,
      (metricDomain[1] / 3) * 2,
      metricDomain[1],
    ])
    .range(["#f4f4f4", "#89BC97", "#5D2EDD", "#000"])
    .interpolate(d3.interpolateHclLong);

  const maxLength = 8;
  $: scaleScale = d3.scaleSqrt().domain(metricDomain).range([0, maxLength]);
  $: colorScaleTicks = colorScale.ticks(6).map((d) => {
    const scale = scaleScale(d);
    const tickHeight = (height / 100) * scale;

    return {
      tickHeight,
      value: d,
      percent: (d * 100) / metricDomain[1],
      color: colorScale(d),
    };
  });

  $: radiusScale = d3.scaleLinear().domain(timeDomain).range([20, 40]);
  $: yearRadiusScale = d3
    .scaleLinear()
    .domain(timeDomain)
    .range([height * 0.21, height * 0.41]);
  $: strokeWidthScale = d3.scaleLinear().domain(timeDomain).range([0.3, 0.65]);
  $: angleScale = d3.scaleLinear().domain([0, 365]).range([0.3, 0.65]);

  const getPositionFromDistanceAndAngle = (distance, angle) => {
    const x = distance * Math.cos((angle * Math.PI) / 180);
    const y = distance * Math.sin((angle * Math.PI) / 180);
    return { x, y };
  };

  const getTransformFromDistanceAndAngle = (distance, angle) => {
    const { x, y } = getPositionFromDistanceAndAngle(distance, angle);
    return `translate(${x}, ${y})`;
  };

  const getPathForValue = (value) => {
    const height = scaleScale(value);
    return ["M", 0, -height / 2, "L", 0, height / 2].join(" ");
  };

  const monthNames = range(0, 12).map((i) =>
    d3.timeFormat("%b")(new Date(2000, i, 1))
  );
  $: months = monthNames.map((month, i) => {
    const angle = (360 / 12) * i - 360 / 4;
    const { x, y } = getPositionFromDistanceAndAngle(
      width * (i ? 0.38 + i * 0.009 : 0.49),
      angle
    );
    return {
      i,
      name: month,
      angle,
      x: x + width / 2,
      y: y - height / 2,
    };
  });
</script>
