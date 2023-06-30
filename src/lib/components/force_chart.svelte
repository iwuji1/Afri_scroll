<script>
//importing libraries
import * as d3 from 'd3';
import { scaleBand, scaleLinear } from "d3-scale";
import { max, mean, rollups } from "d3-array";
import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
import { fade } from "svelte/transition";

//importing components
import Data from "$lib/Data/inde_flags.json";
import Cols from "$lib/Data/Colonizer.js";
import AxisX from "$lib/components/AxisX.svelte";
import AxisY from "$lib/components/AxisY.svelte";
import Legend from "$lib/components/force_legend.svelte";
import Tooltip from "$lib/components/force_tooltip.svelte";
import Btext from "$lib/Data/cluster_text.js";
import Scrolly from "$lib/helpers/Scrolly.svelte";


//definitions
let width = 800;
let height = 600;
let hovered;
let hoveredColonist;
let currentStep;
let groupByColonist = false;
const margin = { top: 20, right: 20, left: 120, bottom: 20 };

const RADIUS = 6;

$: innerWidth = width - margin.left - margin.right;
let innerHeight = height - margin.top - margin.bottom;


const periodgroups = ["Stiring the pot","The Bridge","Liberation from the west","We still going","Another jump","The new commers & Future"];
const subgroups = ["United States", "Britain", "Italy", "Britain/Egypt", "France", "Spain", "Belgium", "Portugal", "South Africa", "Ethiopia"];

$: xScale = scaleBand()
  .domain(periodgroups)
  .range([0, innerWidth])
  .paddingOuter(0.5);

// Generate the average for each colonizer, so that we can sort according to that
const colonizers = rollups(
    Cols,
    v => mean(v, d => d.Number_of_colonies),
    d => d.Colonizer
  ) // Group data by colonizer and return the group-wide mean
  .sort((a, b) => a[1] - b[1]) // Sort according to value
  .map(d => d[0]); // Grab the colonizer name

let yScale = scaleBand()
  .domain(colonizers)
  .range([innerHeight, 0])
  .paddingOuter(0.5);

$: cScale = d3.scaleOrdinal().domain(subgroups).range(["#b22234","#012169","#009246","#C09300","#ED2939","#F1BF00","#2D2926","#046A38","#FFB81C","#0645B1","#7f3b08"]);

const simulation = forceSimulation(Data); // Instantiate a barebones simulation
let nodes = [];
simulation.on("tick", () => {
  nodes = simulation.nodes();
});
// Run the simulation whenever any of the variables inside of it change
$: {
simulation
    .force("x", forceX()
        .x(d => xScale(d.Theme))
        .strength(0.8)
    )
    // .force("y", forceY()
    //     .y(d => yScale(d.Colonizer))
    //     .strength(0.2)
    // )
    .force("y",
    forceY()
        .y(d =>
        groupByColonist
            ? yScale(d.Colonizer)
            : innerHeight / 2
        )
        .strength(0.2)
    )
    .force("collide", forceCollide().radius(RADIUS))
    .alpha(0.3) // [0, 1] The rate at which the simulation finishes. You should increase this if you want a faster simulation, or decrease it if you want more "movement" in the simulation.
    .alphaDecay(0.0005) // [0, 1] The rate at which the simulation alpha approaches 0. you should decrease this if your bubbles are not completing their transitions between simulation states.
    .restart(); // Restart the simulation
}

$: {
  if (currentStep === 0) {
    // Set Colonies to 0 on initial check
    groupByColonist = false;
  } else if (currentStep === 1) {
      // increase by a bit to test
      groupByColonist = false;

  } else if (currentStep === 2) {
      // Set all data to defaults
      groupByColonist = true;
  }
}

</script>

<style>

  :global(.tick text, .axis-title) {
      font-size: 12px; /* How big our text is */
      font-weight: 400; /* How bold our text is */
      fill: hsla(212, 10%, 53%, 1); /* The color of our text */
      user-select: none; /* Prevents text from being selected */
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
    font-size: 1.35rem; /* How big our text is */
    margin: 0 0 0.5rem 0; /* Adds a bottom margin */
    font-weight: 600; /* How bold our text is */
    text-align: center;
  }

  p {
    font-size: 1.5em;
    margin: 20%;
    text-align: center;

    font-family: sans-serif;
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
</style>
<div class="sticky">
<!-- <h1>Independence of African Nations</h1> -->
<div class='chart-container'
bind:clientWidth={width}>

<Legend {cScale} bind:hoveredColonist />
  <svg {width} {height}>
    <g class="inner-chart" transform="translate({margin.left}, {margin.top})" on:mouseleave={() => (hovered = null)}>>
      <AxisX {xScale} height={innerHeight} width={innerWidth} />
      <AxisY {yScale} {groupByColonist}/>
      {#each nodes as node}
        <circle
        cx={node.x}
        cy={node.y}
        r={5}
        fill={cScale(node.Colonizer)}
        stroke={hovered || hoveredColonist
            ? hovered === node || hoveredColonist === node.Colonizer
              ? "black"
              : "transparent"
            : "#00000090"}
        opacity={hovered || hoveredColonist
            ? hovered === node || hoveredColonist === node.Colonizer
              ? 1
              : 0.3
            : 1}
        on:mouseover={() => hovered = node}
        on:focus={() => hovered = node}
        tabindex="0" />
      {/each}
    </g>
  </svg>
  {#if hovered}
    <Tooltip data={hovered} {cScale} {width}/>
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
