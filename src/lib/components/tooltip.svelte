<script>
  export let data;
  export let xScale;
  export let yScale;
  export let width;
  export let cScale;

  import { fly, fade } from "svelte/transition"

  let tooltipWidth;

  const xNudge = 5;
  const yNudge = 5;

  // If the x position + the tooltip width exceeds the chart width, offset backward to prevent overflow
  $: xPosition =
    xScale(data.Number_of_colonies) + tooltipWidth + xNudge > width
    ? xScale(data.Number_of_colonies) - tooltipWidth - xNudge
    : xScale(data.Number_of_colonies) + xNudge;

  $: yPosition = yScale(data.Colonizer) + yNudge;
</script>

<style>

  .tooltip{
    position: absolute;
    padding: 8px 6px;
    background: white;
    box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px;
    border-radius: 3px;
    pointer-events: none;
    min-width: 130px;
    transition: top 300ms ease, left 300ms ease;
  }

  h1 {
      margin: 0;
      font-size: 1rem;
      font-weight: 500;
      margin-bottom: 3px;
  }

  h3 {
    font-weight: 700;
    font-family:sans-serif;
    margin-bottom: 0.1rem;
  }

  .info {
    display: flex;
    justify-content: space-between;
    column-gap: 8px;
  }

  .cols {
    font-size: 0.8rem;

  }

  .colonizer {
    font-size: 0.65rem;
    color: #ffffff;
    padding: 3px 4px 2px 4px;
    border-radius: 3px;
    text-transform: uppercase;
    white-space: nowrap;
  }

</style>

<div
  class="tooltip"
  in:fly={{ y: 10, duration: 200, delay: 200 }}
  out:fade
  style="left:{xPosition}px; top:{yPosition}px;"
  bind:clientWidth={tooltipWidth}>
  <!-- Country name -->
  <h1>
    {data.Colonizer}
  </h1>
  <!-- Additional info under the country name -->
  <div class='info'>
      <span class="colonizer" style="background: {cScale(data.Colonizer)}">
          {data.Number_of_colonies}
      </span>
  </div>
</div>
