<script>
  import * as d3 from "d3";

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
</script>
