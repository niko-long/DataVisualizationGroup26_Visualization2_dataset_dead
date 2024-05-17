<script>
  import Defs from './Defs.svelte';
  import YearLabels from './YearLabels.svelte';
  import CentralLineChart from './CentralLineChart.svelte';
  import CountryLabels from './CountryLabels.svelte';
  import CountryHighlighter from './CountryHighlighter.svelte';
  import IsoDetector from './IsoDetector.svelte';

  export let width;
  export let height;
  export let offset;
  export let data;
  export let years;
  export let scCountryAngle;
  export let scYearRadius;
  export let scCartPriceInCP;
  export let scMortRate;
  export let selectedIso;

  $: innerRadius = scYearRadius(years[0]) * 0.62;
  $: countryRadius = scCartPriceInCP.range()[1];
</script>

<svg class="svg-visual"
     width={width}
     height={height}
     style="margin: {offset / 2}px;">
  <Defs scCartPriceInCP={scCartPriceInCP} />
  <YearLabels width={width}
              height={height}
              years={years}
              scYearRadius={scYearRadius} />
  <CentralLineChart width={width}
                    height={height}
                    data={data}
                    selectedIso={selectedIso}
                    radius={innerRadius} />
  <CountryLabels width={width}
                 height={height}
                 data={data.map(d => ({Territory: d.Territory, Territory: d.Territory}))}
                 scCountryAngle={scCountryAngle}
                 radius={countryRadius}
                 selectedIso={selectedIso} />
  <CountryHighlighter width={width}
                      height={height}
                      data={data}
                      years={years}
                      scCountryAngle={scCountryAngle}
                      scYearRadius={scYearRadius}
                      scMortRate={scMortRate}
                      scCartPriceInCP={scCartPriceInCP}
                      selectedIso={selectedIso} />
  <IsoDetector width={width}
               height={height}
               radius={scCartPriceInCP.range()[1]}
               scCountryAngle={scCountryAngle}
               selectedIso={selectedIso}
               on:isochanged />
</svg>

<style>
  svg {
    position: absolute;

  }
</style>
