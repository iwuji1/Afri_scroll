<svelte:head>

</svelte:head>

<script>
  //d3 map essentials
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import * as topojson from "topojson-client";

  //import components
  import Tooltip from "$lib/components/map_tooltip.svelte";
  import MapLegend from "$lib/components/map_legend.svelte";
  import maptext from "$lib/Data/map_text.js";
  import Scrolly from "$lib/helpers/Scrolly.svelte";

  //colour scale
  import { max } from "d3-array";
  import { scaleBand, scaleLinear } from "d3-scale";

  //data imports
  import world from "$lib/Data/country_data.json";
  import world2 from "$lib/Data/countries-10m.json";
  import afshades from "$lib/Data/africa.json";

  //geopaths
  import { geoOrthographic, geoPath, geoNaturalEarth1, geoMercator} from "d3-geo";
  import * as d3_composite from "d3-composite-projections";

  let countries = topojson.feature(world, world.objects.countries).features;
  let borders = topojson.mesh(world, world.objects.countries, (a, b) => a !== b);

  // let afcountries = topojson.feature(afrigeo, afrigeo.features).features;

  let width = 1000;
  let height = 800;
  let currentStep;
  let hoverData;
  const subgroups = ["United_States", "Britain", "Italy", "Britain_Egypt", "France", "Spain", "Belgium", "Portugal", "South_Africa", "Ethiopia"];

  $: projection = geoMercator()
    .scale(width / 3.5)
    .rotate([0, 0, 0])
    .translate([width / 2, height / 2]);

  $: path = geoPath(projection); // This is new!
  $: cScale = d3.scaleOrdinal().domain(subgroups).range(["#b22234","#012169","#009246","#C09300","#ED2939","#F1BF00","#2D2926","#046A38","#FFB81C","#0645B1","#7f3b08"])


</script>

<style>

  :global(body) {
    font-family: Helvetica, sans-serif;
  }

  .map-container {
    position: relative;
    width: 100%;
    height: 100%;
    font-family: sans-serif;
  }

  .sticky {
    position: sticky; /* make the element sticky */
    height: 100vh; /* 90% of the viewport height */
    top: 5vh; /* (100vh - 90vh) /2 */
    width: 100%; /* Full width */
    margin-bottom: 1rem; /* Add some space between the chart and the text */
    display: flex;
    align-items: center;
    justify-content: center;
  }

  p {
    font-size: 1.5em;
    text-align: center;

    font-family: sans-serif;
  }


  svg {
    position: absolute;
    z-index: 1;
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
    padding: 0.75rem 1rem;
    border: 1px solid black;
    border-radius: 3px;
    width: 50%;
    background: white;
  }
</style>

<div class="sticky">

<!-- {#if hoverData}
  <Tooltip data={hoverData} {cScale} {width}/>
{/if} -->

  <div class="map-container"
    bind:clientWidth={width}>
      <MapLegend data={subgroups} {hoverData} {cScale}/>
    <svg width="100vw" height="90vh" on:mouseleave={() => (hoverData = null)}>
      <rect width="100vw" height="90vh" fill="#74ccf4"/>
      <!-- Countries -->

      {#each countries as country}
          <path d={path(country)}
          fill="lightgreen"
          stroke="white" />
      {/each}
      {#each afshades as d}
        {#each countries as country}
          {#if d.properties.id == country.id}
            <path d={path(country)}
            fill={cScale(d.properties.colonizer)}
            stroke="white"
            opacity={hoverData ? hoverData == d ? "1":".3" : "1"}
            on:mouseover ={() => hoverData = d}
            on:focus ={() => hoverData = d}
            tabindex="0"/>
          {/if}
        {/each}
      {/each}


    </svg>
  </div>
</div>

<div class="steps">
  <Scrolly bind:value={currentStep}>
      {#each maptext as text, i}
          <div class="step" class:active={currentStep === i}>
              <div class="step-content">
                  <p>{text.Content}</p>
              </div>
          </div>
      {/each}
  </Scrolly>
</div>
