<script>
  import * as d3 from 'd3';
  import { scaleBand, scaleLinear } from "d3-scale";
  import { max } from "d3-array";

  import { tweened } from "svelte/motion";
  import { cubicOut } from "svelte/easing";

  import Data from "$lib/Data/Colonizer.js";
  import Btext from "$lib/Data/bar_text.js";
  import Tooltip from '$lib/components/tooltip.svelte';

  import Scrolly from "$lib/helpers/Scrolly.svelte";

  let currentStep;

  let hoverData;

  let width = 800;
  let height = 600;
  const subgroups = ["United States", "Britain", "Italy", "Britain/Egypt", "France", "Spain", "Belgium", "Portugal", "South Africa", "Ethiopia"];

  const margin = { top: 20, right: 20, left: 120, bottom: 20 };
  $: innerHeight = height - margin.top - margin.bottom;
  $: innerWidth = width - margin.left - margin.right;

  $: xDomain = Data.map(function(d) {return d.Number_of_colonies});
  $: yDomain = Data.map(function(d) {return d.Colonizer});

  $: yScale = scaleBand().domain(yDomain).range([0, innerHeight]).padding(0.1);
  $: xScale = scaleLinear().domain([0,max(Data, function(d) {return d.Number_of_colonies})]).range([0, innerWidth]);
  $: cScale = d3.scaleOrdinal().domain(subgroups).range(["#b22234","#012169","#009246","#C09300","#ED2939","#F1BF00","#2D2926","#046A38","#FFB81C","#0645B1","#7f3b08"])

  let initialData = Data.sort((a,b) => a.grade - b.grade);
  let renderedData = initialData;

  $: {
    if (currentStep === 0) {
      // Set Number of colonies to 0
        renderedData = initialData.map(d => ({ ...d, Number_of_colonies: 0 }));
    } else if (currentStep === 1) {
        // Make an initial rise in colonies
        renderedData = initialData.map(d => ({...d, Number_of_colonies: 5 }));
    } else if (currentStep === 2) {
        // Set all data to defaults
        renderedData = initialData;
    }
  }

</script>

<style>

  .chart-container {
    position: relative;
    height: 100%;
    width: 100%;

    /* background: white;
    box-shadow: 1px 1px 30px rgba(252, 220, 252, 1);
    border: 1px solid plum;
    border-radius: 6px; */
  }

  .sticky {
    position: sticky; /* make the element sticky */
    height: 90vh; /* 90% of the viewport height */
    top: 5vh; /* (100vh - 90vh) /2 */
    width: 60%; /* Full width */
    margin-bottom: 1rem; /* Add some space between the chart and the text */
    margin-left: 10%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  rect {
    transition:
      width 300ms cubic-bezier(0.76, 0, 0.24, 1),
      opacity 300ms ease,
      fill 300ms ease;
    cursor: pointer;
  }

  rect:focus {
    outline: none;
  }

  h1 {
    font-size: 30px;
    font-weight: 600;
    margin-bottom: 0.5rem;
    font-family: sans-serif;
  }

  p {
    font-size: 1.5em;
    margin: 20%;
    text-align: center;

    font-family: sans-serif;
  }

  svg {
    font-family: sans-serif;
  }

  section {
    position: relative;
  }

  @media screen and (max-width: 1000px) {
    svg {
      width: 100%;
      height: auto;
    }

    .sticky {
      width: 100%;
      margin: auto;
    }

    p {
      margin: auto;
    }

    .step-content {
      padding: 0.75rem 1rem;
      border: 1px solid black;
      border-radius: 3px;
      width: 50%;
      background: white;
    }
  }

  .steps {
    z-index: 2;
    position: relative;
    pointer-events: none;
  }

  .step {
      height: 90vh;
      opacity: 0.3;
      transition: opacity 300ms ease;
      display: flex;
      justify-content: center;
      place-items: center;
    }

  .step.active {
      opacity: 1;
  }

  /* .step-content {
    padding: 0.75rem 1rem;
    border-radius: 3px;
    background: white;
  } */

</style>

<div class="sticky">
  <div class="chart-container"
    bind:clientWidth={width}
    on:mouseleave={() => {
      hoverData = null;
      }}>
    <svg {width} {height}>
      <g transform={`translate(${margin.left},${margin.top})`}>
        {#each xScale.ticks() as tickValue}
          <g transform={`translate(${xScale(tickValue)},0)`}>
            <line y2={innerHeight} stroke="#cecece" />
            <text text-anchor="middle" dy=".7em" y={innerHeight + 3}>
              {tickValue}
            </text>
          </g>
        {/each}
        {#each renderedData as d,i}
            <text text-anchor="end" x="-3" dy=".3em" y={yScale(d.Colonizer) + yScale.bandwidth() / 2}>
              {d.Colonizer}
            </text>
            <rect
              class="bars"
              x="0"
              y={yScale(d.Colonizer)}
              width={xScale(d.Number_of_colonies)}
              height={yScale.bandwidth()}
              opacity={hoverData ? hoverData == d ? "1":".3" : "1"}
              fill={hoverData && hoverData == d ?"#F7931E":cScale(d.Colonizer) }
              on:mouseover={() => {
                hoverData = d
                }}
              on:focus={() => {
                hoverData = d
                }}
                tabIndex="0"
              />
          {/each}
      </g>
    </svg>
    {#if hoverData}
      <Tooltip data={hoverData} {xScale} {yScale} {width} {cScale}/>
    {/if}
  </div>
</div>

<div class="steps">
  <Scrolly bind:value={currentStep}>
      {#each Btext as text, i}
          <div class="step" class:active={currentStep === i}>
              <div class="step-content">
                  <p>{text.Content}</p>
              </div>
          </div>
      {/each}
  </Scrolly>
</div>
