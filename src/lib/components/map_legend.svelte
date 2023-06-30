<script>
  export let cScale;
  export let data;
  export let hoverData;

</script>

<style>

  .legend{
    display: flex;
    justify-content: center;
    flex-direction: row;
    flex-wrap: wrap;
    column-gap: 10px;
    row-gap: 5px;
    margin-bottom: 0.25rem;
    background: #ffffff;
    z-index: 10;
  }

  .chart-title {
    text-align: center;
  }

  h1 {
    font-family: Avenir,system-ui,-apple-system,Helvetica,Arial,sans-serif;
    font-weight: 600;
  }

  p {
    margin: 0;
    font-size: 0.8rem;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    column-gap: 3px;
    cursor: pointer;
    transition: opacity 300ms ease;
  }

  span {
    width: 9px;
    height: 9px;
    display: inline-block;
    border-radius: 50%;
    border: 1px solid rgba(0, 0, 0, 0.5);
  }


</style>

<!-- <div class="legend">
  <h1 class="chart-title">Colonizers</h1>
  <svg width="300" height="280">
    {#each data as d,i}
      <text class="leg-txt" x="120" dy=".3em" y={20 + i*25}>{d}</text>
      <circle
        cx="100"
        cy={20 + i*25}
        r="7"
        opacity={hoverData ? hoverData == d ? "1":".3" : "1"}
        fill={cScale(d)}/>
    {/each}
  </svg>
</div> -->

<div class="legend">
  {#each cScale.domain() as colonist}
    <p>
    <span style="background-color: {cScale(colonist)}" />
      {colonist}
    </p>
  {/each}
</div>
