<script>
  import { range, max, extent } from 'd3-array';
  import { scaleLinear, scaleOrdinal } from 'd3-scale';

  import CanvasVisual from './CanvasVisual.svelte';
  import SVGVisualBelow from './SVGVisualBelow.svelte';
  import SVGVisualOver from './SVGVisualOver.svelte';


  export let data;
  export let years;

  const offset = 10;
  const angleOffset = 0;

  let selectedIso;
  // 用于存储选定ISO编码的变量 

  // Dimensions
  let rawWidth = offset;
  let rawHeight = offset;
  // 这两行定义了一些变量 用于存储图形的原始宽度和高度。

  // Scales
  let scYearColor, scCountryAngle, scYearRadius, scMortRate, scCartPriceInCP, scDeliveryTimeCost;
  //定义变量 用于D3存储比例尺

  function initScales(minDim) {
    scYearColor = scaleOrdinal()
      .domain(years) // 设置了比例尺的 输入域为‘years’
      .range(['rgb(252,123,35)', 'rgb(253, 203, 71)', 'rgb(176, 201, 73)']);//三个年份对应三种颜色

    scCountryAngle = scaleOrdinal()
      //.domain(data.map(d => d.iso))
      .domain(data.map(d => d.Territory))
      .range(range(angleOffset, 2 * Math.PI - angleOffset, (2 * Math.PI - 2 * angleOffset) / data.length));
      // 这行代码将初始化另一个序数比例尺 通过将国家的iso编码映射到角度值 用于在极坐标中确定国家的位置 范围是0~2pi,最后一个是步长

    //scYearRadius = scaleLinear()
    scYearRadius = scaleLinear()
      .domain([years[0], years[years.length-1]]) //分别是数据集中的第一个年份和最后一个年份
      .range([minDim / 5, minDim / 2.4 - padding]);
      //这段代码用于初始化一个线性比例尺 scYearRadius，该比例尺用于将年份映射到在可视化中表示年份的圆环的半径值。

    scMortRate = scaleLinear()
      .domain([0, 1.2 * max([].concat(...data.map(d => d.dataArr.filter(d => years.includes(d.year)).map(d => d.value))))])
      .range([0, minDim / 9]);
      // 创建一个关于Mortrate的比例尺 输入的范围是从0开始到最大死亡率的1.2倍
      // data.map(d => ....)对数据进行集中处理 使用map遍历数据集中每个对象'd', 在每个对象中找到dataArr属性
      // [].concat(...) is used to flatten the 数组 成一维数组
      // max是找展平后的数组的最大值
      

    scCartPriceInCP = scaleLinear()
      .domain(extent(data.map(d => d.CartPriceInCP)))
      .range([Math.min(scYearRadius(years[years.length - 1]) + CartPriceInCPOffset, minDim / 2 - padding), minDim / 2 - padding]);
      // 这里用来设置关于CartPriceInCP的比例尺 extent用来获取计算数组里的最小值和最大值
      // 这里用了之前的scYearRadius来确定图片的圆形的尺寸 在这里应该是最外边的圆
      //应该还没进入画圆的阶段

    scDeliveryTimeCost = scaleLinear()
      .domain(extent(data.map(d => d.DeliveryTimeCost)))
      .range([Math.min(scYearRadius(years[years.length - 1]) + CartPriceInCPOffset, minDim / 2 - padding), minDim / 2 - padding]);
      // 这里用来设置关于CartPriceInCP的比例尺 extent用来获取计算数组里的最小值和最大值
      // 这里用了之前的scYearRadius来确定图片的圆形的尺寸 在这里应该是最外边的圆
      //应该还没进入画圆的阶段
  }

  $: width = rawWidth - offset;
  $: height = rawHeight - offset;
  $: minDim = Math.min(width, height);
  $: padding = minDim / 40;
  $: CartPriceInCPOffset = minDim / 40;


  $: if (data && years) initScales(minDim);
  //$这里的是svelte中的响应式声明(Reactive declaration) 当所依赖的的变量改变时 表达式会被重新计算
</script>

<svelte:body on:click={() => selectedIso = undefined}/>
<!--当用户点击页面时，将 selectedIso 变量设置为 undefined，用于取消当前的选择-->


<div class="wrapper" bind:offsetWidth={rawWidth} bind:offsetHeight={rawHeight}>
  <!--这里的wrapper包裹了下面三个元素offsetWidth 和 offsetHeight 绑定在一起。
    这样一来，当元素的宽度和高度发生变化时，rawWidth 和 rawHeight 也会相应地更新。
  rawwidth以及rawHeight在上文中都等于offset 暂时设置了值为10-->
  {#if (minDim > 0)}
  <SVGVisualBelow width={width}
                  height={height}
                  offset={offset}
                  data={data}
                  years={years}
                  scCountryAngle={scCountryAngle}
                  scYearRadius={scYearRadius}
                  scCartPriceInCP={scCartPriceInCP} />

  <CanvasVisual width={width}
              height={height}
              offset={offset}
              data={data}
              years={years}
              scYearColor={scYearColor}
              scCountryAngle={scCountryAngle}
              scYearRadius={scYearRadius}
              scMortRate={scMortRate}
              selectedIso={selectedIso} />
  <SVGVisualOver width={width}
                 height={height}
                 offset={offset}
                 data={data}
                 years={years}
                 scCountryAngle={scCountryAngle}
                 scYearRadius={scYearRadius}
                 scCartPriceInCP={scCartPriceInCP}
                 scMortRate={scMortRate}
                 selectedIso={selectedIso}
                 on:isochanged={(e) => selectedIso = e.detail} />
                 <!--事件监听器 事件发生后 显示详细信息-->
  {/if}
</div>

<style>
  .info {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    /*--当子元素的总宽度超过容器的宽度时 允许子元素换行显示 */
    align-items: stretch;
    justify-content: space-between;
    /* 定义子元素在主轴上的对齐方式 space-between值使得主元素沿着主轴均匀分布*/
    width: 100%;
    /* 指定容器的宽度是父容器的100% */
    height: auto;
    /* 指定容器的高度根据内容自动调整*/
    color: var(--sand);
  }

  .info > div {
    width: 47%;
    height: 100%;
    margin-bottom: 0;
  }

  @media (max-width: 600px) {
    .info > div {
      width: 100%;
      margin-bottom: 1.5rem;
    }
  }
  /* display: flex;：指定了容器使用 Flex 布局。
  Flex 布局是一种灵活的布局模式，能够使子元素在容器内动态排列，
  并根据需要自动调整它们的大小。*/

  .intro {
    display: flex;
    flex-direction: column;
  }

  .text {
    text-align: justify;
    line-height: 1.7;
  }

  .search {
    display: flex;
    flex-direction: column;
    margin: 2rem 0 0 0;
  }

  .search.small {
    align-items: center;
  }
  
  .data-info {
    font-size: 0.9rem;
    font-style: italic;
    line-height: 1.7;
  }

  .imprint {
    display: flex;
    margin: 1rem 0;
    align-items: center;
    font-size: 0.7rem;
  }

  .imprint img {
    width: 1.7rem;
    margin: 0 0.6rem 0 0;
  }

  .wrapper {
    width: 100%;
    height: 100%;
  }
</style>
