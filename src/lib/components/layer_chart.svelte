<script>
  import Data from "$lib/Data/inde_flags.json";
  import Period from '$lib/components/periods.svelte';

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

  let clickData = {"theme":"1847 - 1950",
"description":"Stiring the pot"};

  // window.onscroll = function() {myFunction()};
  //
  // var header = document.getElementById("intbar");
  // var sticky = header.offsetTop;
  //
  // function myFunction() {
  //   if (window.pageYOffset > sticky) {
  //     header.classList.add("sticky");
  //   } else {
  //     header.classList.remove("sticky");
  //   }
  // }

  let visible = true;

  function toggleVissible() {
      visible = !visible
  }

</script>

<style>

  .div-container {
    display: flex;
    flex-direction: column;
    margin: auto;
    font-family:sans-serif;
    width: 100%;
  }

  .div-container h1 {
    color: #800080;
    font-size: 40px;
  }

  .div-container p {
    font-weight: 400;
    font-size: 30px;
  }


  .flag-spec {
    display: flex;
    background-color: #e08214;
    margin-top: 5%;
    width: 100vw;
    font-family:sans-serif;
  }

  .left-content {
    flex: 50%;
    margin:auto;
    padding-left: 5%;
  }

  .right-content {
    display: grid;
    justify-content: start;
    grid-template-columns: auto auto auto;
    gap: 10px;
  }

  .intro-txt {
    margin-left: 2%;
    margin-right: 5%;
    text-align: center;
  }

  .flag-spec h1{
    font-size: 40px;
    color: #800080;
  }

  .flag-spec p{
    color: #ffffff;
    font-weight: 400;
    font-size: 30px;
  }

  .flag {
    align-self: content;
  }

  img {
    width: 100%;
    height: auto;
  }

  .intbar {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    background: papayawhip;
  }

  .int-link {
    font-weight: bold;
    font-size: 20px;
    text-transform: uppercase;
    text-decoration: none;
    color: #e08214;
    padding: 20px 0px;
    margin: 0px 20px;
    display: inline-block;
    position: relative;
    opacity: 0.75;
  }

  ul {
    list-style: none;
  }

  .image-gallery {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    gap: 10px;
  }

  .image-gallery li {
    flex-basis: 250px; /*width: 350px;*/
    position: relative;
    cursor: pointer;
  }

  .image-gallery::after {
    content: "";
    flex-basis: 250px;
  }

  .image-gallery li img {
    object-fit: cover;
    max-width: 100%;
    height: auto;
    vertical-align: middle;
    border-radius: 5px;
  }

  .overlay {
    position: absolute;
    width: 100%;
    height: 100%;
    background: rgba(57, 57, 57, 0.502);
    top: 0;
    left: 0;
    transform: scale(0);
    transition: all 0.2s 0.1s ease-in-out;
    color: #fff;
    border-radius: 5px;
    /* center overlay text */
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /* hover */
  .image-gallery li:hover .overlay {
    transform: scale(1);
  }

</style>

<div class="div-container">
  <div class= "intro-txt">
    <h1>What are we looking at?</h1>
    <p>What we have been looking at in the project is the reimagining of a table with static dates of African Independence, to something a bit more dynamic
    that tells a story. After going through the previous visualizations, I think a story does come to light, by splitting the various years into specific
    buckets, we can see various themes in the journey to an Independent Africa. Here the years are packaged into year buckets with the corresponding countries
    represented by their flag. Can you guess which countries come under which bucket?
    </p>

    <p><b>Click on the buckets below to find out</b></p>
    <div id="intbar" class="intbar">
      {#each year_themes as t}
          <a class="int-link" on:click={() => {clickData = t}} >{t.description}</a>
      {/each}
    </div>
  </div>

  {#each year_themes as t}
    {#if clickData.description == t.description}
      <div id={t.description} class="flag-spec">
          <div class="left-content">
            <h1>{t.theme}</h1>
            <p>{t.description}</p>
          </div>
            <div>
              <ul class="image-gallery">
              {#each Data as d}
                {#if d.Theme == t.description}
                    <li>
                      <img class="flag" src={d.Img_URL} alt="" />
                      <div class="overlay"><span>{d.Country}</span></div>
                    </li>
                {/if}
              {/each}
              </ul>
            </div>
          </div>
        {/if}
  {/each}
</div>
