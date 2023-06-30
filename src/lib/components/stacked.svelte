<script>
  import * as d3 from 'd3';
  import { scaleBand, scaleLinear } from "d3-scale";
  import { max } from "d3-array";

  import Data2 from "$lib/Data/dates_independence2.js";
  import Legend from '$lib/components/legend_stack.svelte';
  import Scrolly from "$lib/helpers/Scrolly.svelte";
  import Stacktext from "$lib/Data/stack_text.js";

  let hoverData;
  let currentStep;

  let width = 1000;
  let height = 500;
  const subgroups = ["United_States", "Britain", "Italy", "Britain_Egypt", "France", "Spain", "Belgium", "Portugal", "South_Africa", "Ethiopia"]

  const margin = { top: 20, right: 10, left: 80, bottom: 20 };
  const innerHeight = height - margin.top - margin.bottom;
  const innerWidth = width - margin.left - margin.right;
  const stackedData = d3.stack().keys(subgroups)
  const stackedSeries = stackedData(Data2)

  $: xDomain = Data2.map(function(d) {return d.year})

  $: xScale = scaleBand().domain(xDomain).range([0, innerWidth]).padding(0.1);
  $: yScale = scaleLinear().domain([0,15]).range([innerHeight, 0]);
  $: cScale = d3.scaleOrdinal().domain(subgroups).range(["#b22234","#012169","#009246","#C09300","#ED2939","#F1BF00","#2D2926","#046A38","#FFB81C","#0645B1","#7f3b08"])

  let initialData = Data2
  let renderedSeries = stackedData(initialData)

  let test = Data2.map(function(d) {return d})

  $: {
    if (currentStep === 0) {
      // Set Colonies to 0 on initial check
        renderedSeries = stackedData(initialData.map(d => ({ ...d, United_States:0, Britain:0, Italy:0, Britain_Egypt:0,	France:0,	Spain:0, Belgium:0,	Portugal:0,	South_Africa:0,	Ethiopia:0})));
    } else if (currentStep === 1) {
        // increase by a bit to test
        renderedSeries = stackedData(initialData.map(d => ({ ...d, United_States:5, Britain:5, Italy:5, Britain_Egypt:5,	France:5,	Spain:5, Belgium:5,	Portugal:5,	South_Africa:5,	Ethiopia:5})));
    } else if (currentStep === 2) {
        // Set all data to defaults
        renderedSeries = stackedData(initialData)
    }
  }

</script>

<style>
 .chart-container {
   display: flex;
   width: 100%;
   height: auto;
   font-family: sans-serif;
 }

 .sticky {
   position: sticky; /* make the element sticky */
   height: 90vh; /* 90% of the viewport height */
   top: 5vh; /* (100vh - 90vh) /2 */
   width: 100%; /* Full width */
   margin-bottom: 1rem; /* Add some space between the chart and the text */
   display: flex;
   flex-direction: column;
   align-items: center;
   justify-content: center;
 }

 h1 {
   font-size: 30px;
   font-weight: 600;
   margin-bottom: 0.5rem;
   font-family: sans-serif;
   align: left;
 }

 p {
   font-size: 1.5em;
   margin: 20%;
   text-align: center;
   font-family: sans-serif;
 }

 rect {
   transition:
     height 300ms cubic-bezier(0.76, 0, 0.24, 1),
     opacity 300ms ease;
   cursor: pointer;
 }

 rect:focus {
   outline: none;
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

 .step-content {
   /* padding: 0.75rem 1rem;
   border: 1px solid black;
   border-radius: 3px;
   background: white; */
 }

 @media screen and (max-width: 1000px) {
   svg {
     width: 100%;
     height: auto;
     z-index: 1;
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

</style>
<div class="sticky">
  <h1> What years were the peak of independence ?</h1>
  <div class="chart-container"
    bind:clientWidth={width}
    on:mouseleave={() => {
      hoverData = null;
      }}>
    <svg {width} {height}>
      <g transform={`translate(${margin.left},${margin.top})`}>
        {#each yScale.ticks() as tickValue}
          <g transform={`translate(0,${yScale(tickValue)})`}>
            <line x2={innerWidth} stroke="#cecece" />
            <text text-anchor="middle" dy=".7em" y={innerWidth}>
              {tickValue}
            </text>
          </g>
          {/each}
      {#each renderedSeries as d}
        {#each d as ind,i}
          <text class="chart-txt" text-anchor="middle" x={xScale(ind.data.year) + xScale.bandwidth() / 2} dy=".3em" y={innerHeight+10}>{ind.data.year}</text>
          <rect
            class='group-rect'
            y={yScale(ind[1])}
            x={xScale(ind.data.year)}
            width={xScale.bandwidth()}
            height={yScale(ind[0]) - yScale(ind[1])}
            opacity={hoverData ? hoverData == d ? "1":".3" : "1"}
            fill={cScale(d.key)}
            on:mouseover={() => {
              hoverData = d
              }}
            on:focus={() => {
              hoverData = d
              }}
              tabIndex="0"
            />
        {/each}
      {/each}
      </g>
    </svg>
  </div>
</div>

<div class="steps">
  <Scrolly bind:value={currentStep}>
      {#each Stacktext as text, i}
          <div class="step" class:active={currentStep === i}>
              <div class="step-content">
                  <p>{text.Content}</p>
              </div>
          </div>
      {/each}
  </Scrolly>
</div>
