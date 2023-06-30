<script>
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

  let width = 1000;
  let height = 500;

</script>

<style>
  .circle-container {
    display: flex;
    width: 100%;
    height: auto;
    margin: auto;
    /* padding-top: 20%; */
  }

  svg {
    display: block;
    margin: auto;
  }

  .leg-text {
    font-family:sans-serif;
    font-weight: 500;
  }

</style>

<div class="circle-container">
  <svg {width} {height}>
    {#each year_themes as t,i}
      <text class="leg-text" text-anchor="middle" x={220 +i*120} dy=".3em" y="320">
      {t.theme}
      </text>
      <circle
        cx={220 + i*120}
        cy="250"
        r="40"
        fill="#800080"
      />
    {/each}
  </svg>

</div>
