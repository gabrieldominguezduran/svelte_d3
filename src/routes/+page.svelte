<script>
  import LineChart from "../components/charts/Line/LineChart.svelte";
  import Scatter1 from "../components/charts/Scatter/Scatter1.svelte";
  import Scatter2 from "../components/charts/Scatter/Scatter2.svelte";
  import ScatterPlotWrapper from "../components/charts/Scatter/ScatterPlotWrapper.svelte";
  import WarmingStripes from "../components/charts/Stripes/WarmingStripes.svelte";
  import MapWrapper from "../components/charts/WorldMap/MapWrapper.svelte";
</script>

<Scatter1 />
<WarmingStripes />
<LineChart />
<Scatter2 />
<ScatterPlotWrapper />
<MapWrapper />
