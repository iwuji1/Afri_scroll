<script>
    export let cScale;
    export let hoveredColonist;
</script>

<style>
  .legend {
    display: flex;
    justify-content: center;
    flex-direction: row;
    flex-wrap: wrap;
    column-gap: 10px;
    row-gap: 5px;
    margin-bottom: 0.25rem;
  }

  p {
    margin: 0;
    font-size: 0.8rem;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    column-gap: 3px;
    cursor: pointer;
    transition: opacity 300ms ease;
  }

  span {
    width: 9px;
    height: 9px;
    display: inline-block;
    border-radius: 50%;
    border: 1px solid rgba(0, 0, 0, 0.5);
  }

  p.unhovered {
    opacity: 0.3;
  }
</style>

<div class='legend' on:mouseleave={() => (hoveredColonist = null)}>
  {#each cScale.domain() as colonist}
    <p on:mouseover={() => (hoveredColonist = colonist)}
    class:unhovered={hoveredColonist && hoveredColonist !== colonist}>
      <span style="background-color: {cScale(colonist)}" />
      {colonist}
    </p>
  {/each}

</div>
