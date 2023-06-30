<script>
  export let cScale;
  export let data;
  export let height;
  export let hoverData;

</script>

<style>

  .legend{
    background: #FAF9F6;
    width: 20%;
    height: auto;
  }

  @media screen and (max-width: 1200px) {
    .legend {
      position: absolute;
      z-index: 5;
    }
  }

  @media screen and (max-width: 900px) {
    .legend {
      visibility: hidden;
    }
  }

</style>

<div class="legend">
  <svg width="250" height={height}>
    {#each data as d,i}
      <text class="leg-txt" x="120" dy=".3em" y={100 + i*25}>{d.key}</text>
      <circle
        cx="100"
        cy={100 + i*25}
        r="7"
        opacity={hoverData ? hoverData == d ? "1":".3" : "1"}
        fill={cScale(d.key)}/>
    {/each}
  </svg>
</div>
