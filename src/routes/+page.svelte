<script>

  import * as d3 from 'd3';
  import { scaleBand, scaleLinear } from "d3-scale";
  import { max } from "d3-array";

  import Bar from '$lib/components/bar.svelte'
  import Stack from '$lib/components/stacked.svelte'
  import Map from '$lib/components/scroll_map.svelte'
  import Map2 from '$lib/components/d3_map.svelte'
  import Circle from '$lib/components/circles.svelte'
  import Force from '$lib/components/force_chart.svelte'
  import Afimg from '$lib/components/africa_img.svelte'

</script>

<style>
  .title {
    position: absolute;
    font-family:sans-serif;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
  }

  .title h1 {
    background: #ffffff;
  }

  .title p {
    background: #ffffff;
  }

  .title h2 {
    background: #ffffff;
  }

  .title-viz {
    position: relative;
    margin-top: 15%;
  }

  .end_title {
    font-family:sans-serif;
    margin: auto;
  }

  .page-img {
    height: 100%;
  }

  .container {
    display: flex;
    padding-left: 5%;
    margin: auto;
    height: 100vh;
    align-items: center;
    justify-content: center;
  }

  .sticky-container {

  }

  .scroll-container {
    display: flex;
  }

  .scroll-lcontainer {
    display: flex;
    flex-direction: row-reverse;
  }

  @media screen and (max-width: 1000px) {

    .title-viz {
      position: relative;
      margin: auto;
    }


    .container {
      width: 100vw;
    }
    .scroll-container {
      display: flex;
      flex-direction: column;
    }

    .scroll-lcontainer {
      display: flex;
      flex-direction: column;
    }
  }

  h1 {
    font-size: 3em;
  }

  p {
    font-size: 1rem;
  }


</style>

<div class="page-container">
  <section id="Title" class="container">
    <div class="title-viz">
      <Afimg/>
    </div>
    <div class="title">
      <h1>A different look at African independence.</h1>
      <p>I was looking at the dates of African independence for various countries for a video
      I was making. And came across this website with the different dates <a href="https://www.thoughtco.com/chronological-list-of-african-independence-4070467">Awesome resource!</a>.
      </p>
      <p>But it did make me think, there is actually a lot of data in this table, how can I get different insights from this table
      and perhaps visualize African independence in a different way.
      </p>

      <p><b>This is best experienced on the desktop</b></p>

      <h2>I hope you enjoy scrolling!</h2>
    </div>
  </section>

  <section class="scroll-container">
    <Bar />
  </section>

  <section class="scroll-lcontainer">
    <Stack />
  </section>

  <section class="sticky-container">
    <Map2 />
  </section>

  <section class="scroll-container">
    <Force />
  </section>

  <section id="Title" class="panel container">
  <div class="end_title">
    <h1>And that's the end!</h1>

    <h2>Hope you enjoyed the story, here is the link the <a href="https://www.thoughtco.com/chronological-list-of-african-independence-4070467">Original article</a></h2>

  </div>
  </section>


</div>
