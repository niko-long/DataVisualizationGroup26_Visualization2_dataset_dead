<script>
  import { lineRadial, curveCardinal } from 'd3-shape';
  import SVGVisualBelow from './SVGVisualBelow.svelte';

  export let width;
  export let height;
  export let data;
  export let scCountryAngle;
  export let scCartPriceInCP;
  export let scDeliveryTimeCost;

  // the path generator
  $: path = lineRadial() //用于径向线段生成器 将数据点映射到径向线段
    .angle(d => scCountryAngle(d.Territory)) //将数据点的角度映射到极坐标系中
    .radius(d => scCartPriceInCP(d.CartPriceInCP)) // 将数据点的减少值映射到极坐标系中的半径 scCartPriceInCP比例尺将减少的值转换为半径长度
    .curve(curveCardinal); //定义了曲线的差值方式 采用cardinal方式差值
  $: path2 = lineRadial() //用于径向线段生成器 将数据点映射到径向线段
    .angle(d => scCountryAngle(d.Territory)) //将数据点的角度映射到极坐标系中
    .radius(d => scDeliveryTimeCost(d.DeliveryTimeCost)) // 将数据点的减少值映射到极坐标系中的半径 scCartPriceInCP比例尺将减少的值转换为半径长度
    .curve(curveCardinal);//定义了曲线的差值方式 采用cardinal方式差值

</script>

<g transform="translate({width / 2} {height / 2})">
  <circle cx="0"
          cy="0"
          r={scCartPriceInCP(0)}
          stroke="rgb(0, 44, 97, 0.5)"
          stroke-width=20
          stroke-dasharray="2,10"
          fill="none"></circle>

  <path d={path(data)}
        stroke="rgba(0, 44, 97,0.5)"
        fill="url(#CartPriceInCP-gradient)"></path>
  
 
</g>

<style>
</style>
