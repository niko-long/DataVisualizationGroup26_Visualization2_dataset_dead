<script>
  import { arc as d3arc } from 'd3-shape';
  import { select } from 'd3-selection';

  export let width;
  export let height;
  export let data;
  export let scCountryAngle;
  export let radius;
  export let selectedIso;


  const countryLabelArc = d3arc()
    .startAngle(d => d.angle - Math.PI)
    .endAngle(d => d.angle + Math.PI)
    .innerRadius(d => d.radius)
    .outerRadius(d => d.radius);

  let container;
  let model;

  function update(selectedIso) {
    if (!selectedIso) {
      model = [];
    } else {
      const angle = scCountryAngle(selectedIso);
      const realRadius = radius * (angle > Math.PI / 2 && angle < 1.5 * Math.PI ? 1.04 : 1.02);
      // 这里是调整不同象限的实际的半径的大小 在这里是能够控制国家标签距离圆环的距离
      model = [{
        angle,
        radius: realRadius,
        iso: selectedIso,
        Territory: data.find(d => d.Territory === selectedIso).Territory,
        CartPriceInCP:data.find(d => d.Territory === selectedIso).CartPriceInCP
        // 这里将跟新的数据存储在model里
        // data.find(d => d.iso === selectedIso)：这部分是一个数组查找操作，它使用 Array.find() 
        // 方法查找数组 data 中满足条件的元素，条件是元素的 iso 属性（ISO 代码）等于 selectedIso。
        //.country：一旦找到了符合条件的元素，就从中提取出 country 属性，即国家或地区的名称。
      }];
    }

    select(container).selectAll('.country-label-path')
      .data(model)
      .join('path')
        .attr('class', 'country-label-path')
        .attr('id', d => `country-label-path-${d.Territory}`)
        .attr('d', countryLabelArc)
        .attr('fill', 'none')
        .attr('stroke', 'none');

    select(container).selectAll('.country-label')
      .data(model)
      .join('text')
        .attr('class', 'country-label')
        .append('textPath') // 元素将文本与之前定义的路径进行关联，以沿着路径显示文本。
          .attr('href', d => `#country-label-path-${d.Territory}`)//设置 <textPath> 元素的 href 属性，将其链接到之前定义的路径。
          .attr('font-size', '2rem')
          .attr('text-anchor', 'middle')
          .attr('startOffset', d => `${d.angle > Math.PI / 2 && d.angle < 1.5 * Math.PI ? '75%' : '25%'}`) //设置移动时偏移量
          .attr('fill', 'green')
          .text(d => d.Territory);
  }

  $: if (container) update(selectedIso);
</script>

<g transform="translate({width / 2} {height / 2})" bind:this={container}></g>

<style>
</style>
