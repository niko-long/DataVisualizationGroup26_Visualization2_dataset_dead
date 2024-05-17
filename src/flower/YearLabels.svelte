<script>
  export let width;
  export let height;
  export let years;
  export let scYearRadius;
</script>

<g transform="translate({width / 2} {height / 2})">
  {#each years as year}
    <text transform="translate(0 {-scYearRadius(year) + Math.min(width, height) / 40})">{year}</text>
  {/each}
</g>

<style>
  text {
    fill: #103074;
    font-size: 1.5rem;
    text-anchor: middle;
  }
</style>
