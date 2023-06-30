<script>

  import * as d3 from 'd3';
  import { scaleBand, scaleLinear } from "d3-scale";
  import { onMount } from 'svelte';
  import mapboxgl from "mapbox-gl";
  import 'mapbox-gl/dist/mapbox-gl.css';

  import Legend from '$lib/components/map_legend.svelte';
  import Tooltip from '$lib/components/map_tooltip.svelte';
  import dots from "$lib/Data/africa.json";

  import maptext from "$lib/Data/map_text.js";
  import Scrolly from "$lib/helpers/Scrolly.svelte";

  let map;
  let mapx;
  let mapy;
  let hoverData;
  let currentStep;
  let width = 1000;
  let height = 800;
  const subgroups = ["United_States", "Britain", "Italy", "Britain_Egypt", "France", "Spain", "Belgium", "Portugal", "South_Africa", "Ethiopia"];

  $: cScale = d3.scaleOrdinal().domain(subgroups).range(["#2d004b","#800080","#8073ac","#b2abd2","#d8daeb","#f7f7f7","#fee0b6","#fdb863","#e08214","#b35806","#7f3b08"]);

  mapboxgl.accessToken = 'pk.eyJ1Ijoib2Jpd3VqaSIsImEiOiJja3B3ZHdvenkwMHV4MnFucHc2YW9tcHcyIn0.wI9Kn7vACQdq61dnjStghg';

  onMount(() => {
    map = new mapboxgl.Map({
        container: 'map', // container id
        style: 'mapbox://styles/mapbox/streets-v9',
        center: [15, -6],
        liglatbounds: ([-73.9876, 40.7661], [-73.9397, 40.8002]),
        zoom: 2.5,
        interactive: false,
    });

    // Projection method:
    // Project geojson coordinate to the map's current state
  })

</script>

<style>
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

  #map {
    position: absolute;
    width: 100%;
    height: 100%;
    padding: 0;
    margin: 0;
  }

  @media screen and (max-width: 1000px) {
    /* #map {
      width: 100%;
    } */
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
  <div class="map-container"
    bind:clientWidth={width}
    on:mouseleave={() => {
      hoverData = null;
      }}>
    <div id="map"></div>
      <svg width="100%" {height}>
      {#each dots as d}
        {#if map}
          <circle class="map-dots"
            cx={map.project(d.geometry.coordinates).x}
            cy={map.project(d.geometry.coordinates).y}
            r="5"
            fill={cScale(d.properties.colonizer)}
            stroke={hoverData && hoverData == d ?"#F7931E":"#800080" }
            stroke-width={hoverData && hoverData == d ?"2":"0" }
            on:mouseover={() => {
              hoverData = d
              mapx = map.project(d.geometry.coordinates).x
              mapy = map.project(d.geometry.coordinates).y
              }}
            on:focus={() => {
              hoverData = d
              mapx = map.project(d.geometry.coordinates).x
              mapy = map.project(d.geometry.coordinates).y
              }}
          />
        {/if}
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
