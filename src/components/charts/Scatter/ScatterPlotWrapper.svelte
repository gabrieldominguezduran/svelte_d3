<script>
  import SimplexNoise from "simplex-noise";
  import Scatterplot from "./Scatterplot.svelte";

  const simplex = new SimplexNoise(0);

  let interation = 3;

  const createData = () => {
    interation = interation + 1;

    return new Array(300)
      .fill(0)
      .map((_, i) => [i, i % interation, simplex.noise2D(i, i % interation)]);
  };

  let data = createData();
</script>

<Scatterplot />
