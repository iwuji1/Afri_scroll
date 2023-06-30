<script>
  import * as d3 from 'd3';
  import { scaleBand, scaleLinear } from "d3-scale";
  import { max } from "d3-array";

  import Data from "$lib/Data/inde_flags.json";
  import Btext from "$lib/Data/cluster_text.js";
  import Scrolly from "$lib/helpers/Scrolly.svelte";

  const year_themes = [
    {"theme":"1847 - 1950",
  "description":"Stiring the pot"},
    {"theme":"1951 - 1959",
  "description":"The Bridge"},
    {"theme":"1960",
  "description":"Liberation from the west"},
    {"theme":"1961 - 1972",
  "description":"We still going"},
    {"theme":"1973 - 1989",
  "description":"Another jump"},
    {"theme":"1990 - Present",
  "description":"The new commers & Future"}
  ]

  let clickData = {"theme":"1847 - 1950", "description":"Stiring the pot"};
  const periodgroups = ["Stiring the pot","The Bridge","Liberation from the west","We still going","Another jump","The new commers & Future"]
  let width = 800;
  let height = 600;

  const margin = { top: 40, right: 20, left: 120, bottom: 20 };
  $: innerHeight = height - margin.top - margin.bottom;
  $: innerWidth = width - margin.left - margin.right;

  let hoverData;
  let currentStep;

  $: cScale = d3.scaleOrdinal().domain(periodgroups).range(["#b22234","#012169","#009246","#C09300","#ED2939","#F1BF00","#2D2926","#046A38","#FFB81C","#0645B1","#7f3b08"]);

  $: xScale = scaleBand().domain(periodgroups).range([0, innerWidth]).padding(0.1);

  var xCenter = [100, 200, 300, 400, 500, 600];

  var newData = Data.map(function(d,i) {
    return {
      country: d.Country,
      colonizer: d.Colonizer,
      YOI: d['Year of Independence'],
      theme: d.Theme,
      period: d.Period,
      img: d.Img_URL,
      radius: Math.random() * 25
    }
  });

  let forceData = [];

  let initialData = newData

  let simulation = d3.forceSimulation(initialData);

  function update() {
    forceData = initialData
    return initialData
  }

  $: simulation.force('charge', d3.forceManyBody().strength(2));
  $: simulation.force('x', d3.forceX().x(function(d) {
		return xScale(d.theme)
	}));
  $: simulation.force('collision', d3.forceCollide().radius(function(d) {
		return d.radius
	}));
  $: simulation.on('tick', update);

  let renderedData = initialData;

  $: {
    if (currentStep === 0) {
      // Set Number of colonies to 0
        renderedData = initialData.map(d => ({ ...d, x:120, y:0 }));
    } else if (currentStep === 1) {
        // Make an initial rise in colonies
        renderedData = initialData.map(d => ({...d, x:100}));
    } else if (currentStep === 2) {
        // Set all data to defaults
        renderedData = initialData;
    }
  }

</script>

<style>
  :global(.tick text, .axis-title) {
    font-weight: 400; /* How thick our text is */
    font-size: 12px; /* How big our text is */
    fill: hsla(212, 10%, 53%, 1); /* The color of our text */
  }

  .chart-container {
    position: relative;
    height: 100%;
    width: 100%;
    max-width: 700px;
    max-height: 450px;

    /* background: white;
    box-shadow: 1px 1px 30px rgba(252, 220, 252, 1);
    border: 1px solid plum;
    border-radius: 6px; */
    align-items: center;
    justify-content: center;
  }

  circle {
    transition: r 300ms ease, opacity 500ms ease;
    cursor: pointer;
  }

  h1 {
    margin: 0.5rem 0 1rem 0;
    font-size: 1.35rem;
    font-weight: 600;
  }

  p {
    font-size: 1.5em;
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
      width: 80%;
    }

  .step.active {
      opacity: 1;
  }

  /* .step-content {
    padding: 0.75rem 1rem;
    border: 1px solid black;
    border-radius: 3px;
    background: white;
  } */

  .sticky {
    position: sticky; /* make the element sticky */
    height: 90vh; /* 90% of the viewport height */
    top: 5vh; /* (100vh - 90vh) /2 */
    width: 60%; /* Full width */
    margin-bottom: 1rem; /* Add some space between the chart and the text */
    display: flex;
    align-items: center;
    justify-content: flex-end;
  }

  circle {
    transition: r 300ms ease,
        opacity 500ms ease,
        cx 500ms cubic-bezier(0.76, 0, 0.24, 1),
        cy 500ms cubic-bezier(0.76, 0, 0.24, 1);
  }

  #image {

  }

</style>

  <div class="sticky">
    <div
      class="chart-container"
      bind:clientWidth={width}
      bind:clientHeight={height}>
      <svg {width} {height}>
        <g class="inner-chart" transform="translate(200, 200)">
          {#each periodgroups as ticks,i}
            <g transform={`translate(${i*100},50)`}>
              <text text-anchor="middle" dy=".7em" y={innerHeight + 3}>
                {ticks}
              </text>
            </g>
          {/each}
            {#each renderedData as d, i}
              <defs>
                <pattern id="image" patternUnits="userSpaceOnUse" height="100" width="100">
                  <image x="0" y="0" height="100" width="100" xlink:href={d.img}></image>
                </pattern>
              </defs>
              <circle
                class={d.country}
                cx={d.x}
                cy={d.y}
                fill={cScale(d.period)}
                stroke="black"
                r={15}
              />
            {/each}
            </g>
      </svg>
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
