<script>
  export let data;
  export let xScale;
  export let yScale;
</script>

<div
  class="tooltip"
  style="position: absolute;
  top: {yScale(data.hours)}px;
  left: {xScale(data.grade)}px"
>
  <h4>{data.name}</h4>
  <p>{data.hours} hours studied</p>
</div>

<style>
  .tooltip {
    padding: 0.3rem;
    background: rgba(208, 207, 207, 0.742);
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
