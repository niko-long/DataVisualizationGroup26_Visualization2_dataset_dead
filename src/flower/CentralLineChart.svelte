<script>
  import { extent, max } from 'd3-array';
  import { scaleLinear } from 'd3-scale';
  import { line as d3line, curveCardinal } from 'd3-shape';

  export let width;
  export let height;
  export let data;
  export let selectedIso;
  export let radius;

  let dataArr,dataArr2, xScale,xScale2, yScale,yScale2, line,line2, yLabels,yLabels2,yLabels3;
  let Territory, CartPriceInCP, AccountNum, Area, Product,Quantities;
  function updateScalesAndGenerators(radius) {

    xScale = scaleLinear()
      .domain(extent([].concat(...data.map(d => d.dataArr)).map(d => d.year)))
      .range([-radius / 1.5, radius / 1.5]);

    yScale = scaleLinear()
      .domain([-10, max([].concat(...data.map(d => d.dataArr)).map(d => d.value))])
      .range([radius / 2, -radius / 2]);
      
    line = d3line()
      .x(d => xScale(d.year))
      .y(d => yScale(d.value))
      .curve(curveCardinal);

    yLabels = [
      {
        x: xScale(dataArr[0].year) * 1.05,
        y: yScale(dataArr[0].value) + Math.min(width, height) / 200,
        text: Math.round(dataArr[0].value),
        textAnchor: 'end'
      },
      {
        x: xScale(dataArr[dataArr.length - 1].year) * 1.05,
        y: yScale(dataArr[dataArr.length - 1].value) + Math.min(width, height) / 200,
        text: Math.round(dataArr[dataArr.length - 1].value),
        textAnchor: 'start'
      }
    ];
  }

  function updateScalesAndGenerators2(radius) {

    xScale2 = scaleLinear()
      .domain(extent([].concat(...data.map(d => d.dataArr2)).map(d => d.year)))
      .range([-radius / 1.5, radius / 1.5]);

    yScale2 = scaleLinear()
      .domain([-10, max([].concat(...data.map(d => d.dataArr2)).map(d => d.value))])
      .range([radius / 2, -radius / 2]);
      
    line2 = d3line()
      .x(d => xScale2(d.year))
      .y(d => yScale2(d.value))
      .curve(curveCardinal);
    
    

    yLabels2 = [
      {
        x: xScale2(dataArr2[0].year) * 1.05,
        y: yScale2(dataArr2[0].value) + Math.min(width, height) / 200,
        text: Math.round(dataArr2[0].value),
        textAnchor: 'end'
      },
      {
        x: xScale2(dataArr2[Math.floor((dataArr2.length+1) / 2)].year) * 0.55,
        y: yScale2(dataArr2[Math.floor((dataArr2.length+1) / 2)].value+20) + Math.min(width, height) / 200,
        text: Math.round(dataArr2[Math.floor(dataArr2.length / 2)].value),
        textAnchor: 'middle'  // Adjust as needed
      },
      {
        x: xScale2(dataArr2[dataArr2.length - 1].year) * 1.05,
        y: yScale2(dataArr2[dataArr2.length - 1].value) + Math.min(width, height) / 200,
        text: Math.round(dataArr2[dataArr2.length - 1].value),
        textAnchor: 'start'
      }
    ];


    const selectedData = data.find(d => d.Territory === selectedIso);

    if (selectedData) {
      CartPriceInCP = selectedData.CartPriceInCP;
      AccountNum = selectedData.AccountNum;
      Area = selectedData.Area;
      Product = selectedData.Product;
      Quantities = selectedData.Quantities;
      Territory = selectedData.Territory;

      console.log('CartPriceInCP:', CartPriceInCP); // Debug: Check each value
      console.log('AccountNum:', AccountNum);
      console.log('Area:', Area);
      console.log('Product:', Product);
    } else {
      CartPriceInCP = AccountNum = Area = Product = undefined;
    };
  }

  $: if (data && selectedIso) dataArr = data.find(d => d.Territory === selectedIso).dataArr.filter(d => !isNaN(d.value));
  $: if (data && selectedIso) dataArr2 = data.find(d => d.Territory === selectedIso).dataArr2.filter(d => !isNaN(d.value));

  // 这里比较重要 过滤了没有数据的部分?
  $: if (data && dataArr) updateScalesAndGenerators(radius);
  $: if (data && dataArr2) updateScalesAndGenerators2(radius);
</script>

{#if (data && selectedIso)}
<!--这里根据条件块来确定是否显示SVG元素 如果data和 selectediso都存在那么就渲染下面的内容-->
  <g transform="translate({width / 2} {height / 2})">
    <circle cx="0" cy="0" r="240" 
    stroke="#4682B4"
    stroke-width=15
    stroke-dasharray="2,10"
    fill="white"
    fill-opacity="0.6"></circle>
    <text class="title"
          transform="translate(0 {yScale.range()[1] * 1.4})">Sales Per Territory</text>
    <text class="titlesmall"
          transform="translate(0 {yScale.range()[1] * 1.1})">From 2019 to 2023</text>
    <path d={line(dataArr)}
          stroke="orange"
          stroke-width="5"
          fill="none"></path>
    {#each yLabels as yLabel}
      <text class="y-label"
            transform="translate({yLabel.x} {yLabel.y})"
            text-anchor={yLabel.textAnchor}>{yLabel.text}</text>
    {/each}
    <text class="price-label"
    transform="translate({xScale.range()[1] * (-1.25)} {yScale.range()[1] * (0.15)})">PriceInCP</text>
    <line x1={xScale.range()[0]}
          y1={yScale.range()[0]}
          x2={xScale.range()[1]}
          y2={yScale.range()[0]}></line>
    {#each xScale.domain() as xLabel, i}
      <text class="x-label"
            transform="translate({xScale(xLabel)} {yScale.range()[0] * 1.25})"
            text-anchor={i % 2 === 0 ? 'start' : 'end'}>{xLabel}</text>
    {/each}
  </g>
{/if}

{#if (data && selectedIso)}
<!--这里是测试新加相关的delivery time的图--> 
<!--这里根据条件块来确定是否显示SVG元素 如果data和 selectediso都存在那么就渲染下面的内容-->
  <g transform="translate({width / 1.2} {height / 4})">
    <circle cx="0" cy="0" r="260" 
     stroke="#4682B4"
     stroke-width=20
     stroke-dasharray="2,10"
     fill="white"
    fill-opacity="0.6"></circle>
    <text class="title2"
          transform="translate(0 {yScale2.range()[1] * 1.4})">Delivery Time Cost</text>
    <text class="titlesmall2"
          transform="translate(0 {yScale2.range()[1] * 1.1})"> Per Territory</text>
    <path d={line2(dataArr2)}
          stroke="orange"
          stroke-width="5"
          fill="none"></path>
    {#each yLabels2 as yLabel}
      <text class="y-label2"
            transform="translate({yLabel.x} {yLabel.y})"
            text-anchor={yLabel.textAnchor}>{yLabel.text}</text>
    {/each}
    <text class="price-label2"
    transform="translate({xScale.range()[1] * (-1.25)} {yScale.range()[1] * (0.15)})">Days</text>
    <line x1={xScale.range()[0]}
          y1={yScale.range()[0]}
          x2={xScale.range()[1]}
          y2={yScale.range()[0]}></line>
    {#each xScale.domain() as xLabel, i}
      <text class="x-label2"
            transform="translate({xScale(xLabel)} {yScale.range()[0] * 1.25})"
            text-anchor={i % 2 === 0 ? 'start' : 'end'}>{xLabel}</text>
    {/each}
  </g>
{/if}

{#if (data && selectedIso)}
  <image href="/HowToRead.png" x="1" y="200" height="1000" width="1000"></image>

<!--这里是测试新加相关的 仪表盘 风格信息 的图--> 
<!--这里根据条件块来确定是否显示SVG元素 如果data和 selectediso都存在那么就渲染下面的内容-->
  <g transform="translate({width / 1.15} {height / 1.45})">
    <circle cx="0" cy="0" r="260" 
     stroke="#4682B4"
     stroke-width=20
     stroke-dasharray="2,10"
     fill="rgb(255,255,255)"
    fill-opacity="0.5"></circle>
    <text class="detailsLarge" transform="translate({xScale.range()[1] * (-0.5)} {yScale2.range()[1] * 1.4})">DETAILS</text>
      {#if CartPriceInCP !== undefined && AccountNum !== undefined && Area !== undefined && Product !== undefined}
      <text class="details" transform="translate({xScale.range()[1] * (-1)} {yScale2.range()[1] * 0.7})">Territory Name: {Territory}</text>
      <text class="details" transform="translate({xScale.range()[1] * (-1)} {yScale2.range()[1] * 0.3})">Total Sales(InCP): {CartPriceInCP}</text>
      <text class="details" transform="translate({xScale.range()[1] * (-1)} {yScale2.range()[1] * -0.1})">Costumer Number: {AccountNum}</text>
      <text class="details" transform="translate({xScale.range()[1] * (-1)} {yScale2.range()[1] * -0.5})">Belong to Area: {Area}</text>
      <text class="details" transform="translate({xScale.range()[1] * (-1)} {yScale2.range()[1] * -0.9})">TOP Product: {Product}</text>
     {/if}

  </g>
{/if}

<style>
    @font-face {
    font-family: 'digital-7';
    src: url('/digital-7.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
  }
  @font-face {
    font-family: 'advanced_dot_digital-7';
    src: url('/advanced_dot_digital-7.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
  }
  text.title {
    font-size: calc(1.2rem + 0.5vmin);
    text-anchor: middle;
    fill: #103074;
  }
  text.titlesmall {
    font-size: calc(0.8rem + 0.5vmin);
    text-anchor: middle;
    fill: #103074;
  }
  text.price-label {
    font-size: 1rem;
    text-anchor: middle;
    fill: #103074;
  }

  text.y-label {
    font-size: 1rem;
    fill: #103074;
  }
  
  text.x-label {
    font-size: 1rem;
    fill: #103074;
  }
    text.title2 {
    font-size: calc(1.2rem + 0.5vmin);
    text-anchor: middle;
    fill: rgb(0, 44, 97);
  }
  text.titlesmall2 {
    font-size: calc(0.8rem + 0.5vmin);
    text-anchor: middle;
    fill: #103074;
  }
  text.price-label2 {
    font-size: 1.5rem;
    text-anchor: middle;
    fill: rgb(0, 44, 97);
  }

  text.y-label2 {
    font-size: 1.5rem;
    fill: rgb(0, 44, 97);
  }
  
  line {
    stroke: rgb(0, 44, 97);
    stroke-width: 4;
  }

  text.x-label2 {
    font-size: 1.5rem;
    fill: rgb(0, 44, 97);
  }
  text.detailsLarge {
    font-family: 'digital-7',monospace;
    font-size: 2rem;
    fill: #051842;
  }
  text.details {
    font-family: 'digital-7';
    font-size: 1.5rem;
    fill: #103074;
  }

</style>
