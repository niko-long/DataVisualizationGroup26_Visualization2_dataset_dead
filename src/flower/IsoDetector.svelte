<script>
  import { createEventDispatcher } from 'svelte';
  import { bisect } from 'd3-array';

  import { getCentralAngle } from '../utils.js';

  export let width;
  export let height;
  export let radius;
  export let scCountryAngle;
  export let selectedIso;

  const dispatch = createEventDispatcher();
  const eventName = 'isochanged';

  function detectIso(e) {
    const angle = getCentralAngle(e.layerX, e.layerY, width, height);
    const Territory = scCountryAngle.domain()[bisect(scCountryAngle.range(), angle) - 1];
    if (Territory !== selectedIso) dispatch(eventName, Territory);
  }
</script>

<g transform="translate({width / 2} {height / 2})">
  <circle cx="0"
          cy="0"
          r={radius}
          fill="transparent"
          stroke="none"
          on:mousemove={detectIso}
          on:mouseout={() => dispatch(eventName, undefined)}
          on:click|stopPropagation={detectIso}></circle>
</g>

<style>
</style>
