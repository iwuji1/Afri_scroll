<script>
    export let xScale;
    export let height;
    export let width;

    $: ticks = xScale.domain(); // Where 6 is the number of ticks we want to render

</script>

<style>
  text {
    opacity: 50%;
    font-size: 1.5em;
  }

</style>

<g class='axis x'>
  {#each ticks as tick, index}
    <g class='tick'>
      <text x={height/2} y={xScale(tick)} text-anchor="middle" alignment-baseline="hanging" transform="translate(0,{height})rotate(-90)">{tick}</text>
      <line x1={xScale(tick)} x2={xScale(tick)} y1="0" y2={height} stroke="hsla(215, 15%, 91%, 1)"/>
    </g>
  {/each}
</g>
