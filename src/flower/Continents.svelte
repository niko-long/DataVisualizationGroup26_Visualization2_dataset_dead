<script>
  import { arc as d3arc } from 'd3-shape';

  export let width;
  export let height;
  export let data;
  export let years;
  export let scYearRadius;

  const shrinkFactor = 0.70;
  const lineThicknessFactor = 1.05;
  const labelOffsetFactor = 1.1;

  let arc, labelArc;

  // The arcs
  function defineArcs() {
    console.log(data);//检查数据
    //const innerAreaRadius = scYearRadius(years[0]) * shrinkFactor;
    const innerAreaRadius = scYearRadius(years[0]) * shrinkFactor;
    arc = d3arc()
      .startAngle(d => d.startAngle)
      .endAngle(d => d.endAngle)
      .innerRadius(innerAreaRadius)
      .outerRadius(innerAreaRadius * lineThicknessFactor)
      .cornerRadius(7);

    labelArc = d3arc()
      .startAngle(d => d.startAngle)
      .endAngle(d => d.endAngle)
      .innerRadius(innerAreaRadius * labelOffsetFactor)
      .outerRadius(innerAreaRadius * labelOffsetFactor);
  }

  $: if (scYearRadius) defineArcs();
</script>

{#if data}
  <g transform="translate({width / 2} {height / 2})">
    {#each data as d}
      <path class="Area-arc" d={arc(d)}></path>
      <path class="Area-label-arc" id="Area-label-arc-{d.Area}" d={labelArc(d)}></path>
      <text>
        <textPath class="Area-label" href="#Area-label-arc-{d.Area}" startOffset="25%">
          {d.Area}
        </textPath>
      </text>
    {/each}
  </g>
{/if}

<style>
  path.Area-arc {
    stroke-dasharray: 1, 1;
    fill: var(--areaColor);
  }
  path.Area-label-arc {
    fill: none;
  }

  textPath.Area-label {
    fill: #063102;
    text-anchor: middle;
    font-family: MedievalSharp, sans-serif; 
    /*--MedievalSharp 是首选字体。如果用户的设备上安装了这个字体，将会使用它。
sans-serif 是备选字体。如果用户的设备上没有安装 MedievalSharp，浏览器会使用系统默认的 sans-serif 字体。*/
    font-size: 1.8rem;
  }
</style>
