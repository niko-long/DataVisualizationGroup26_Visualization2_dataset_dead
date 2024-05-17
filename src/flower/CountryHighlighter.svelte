<script>
  import { select } from 'd3-selection';
  import { transition } from 'd3-transition';

  export let width;
  export let height;
  export let data;
  export let years;
  export let scCountryAngle;
  export let scYearRadius;
  export let scMortRate;
  export let scCartPriceInCP;
  export let selectedIso;

  let CartPriceInCP;
  let container;
  let modelYears, modelCartPriceInCP;

  function update(selectedIso) {
    if (!selectedIso) {
      modelYears = [];
      CartPriceInCP = 0;
      modelCartPriceInCP = [];
    } else {
      CartPriceInCP = data.find(d => d.Territory === selectedIso).CartPriceInCP;
      modelCartPriceInCP = [{
        cx: Math.sin(Math.PI - scCountryAngle(selectedIso)) * scCartPriceInCP(CartPriceInCP) * (CartPriceInCP <= 0 ? 0.97 : 1.03),
        //根据CartPriceInCP的值来确定CartPriceInCP的指示圈的位置是在内还是在外
        cy: Math.cos(Math.PI - scCountryAngle(selectedIso)) * scCartPriceInCP(CartPriceInCP) * (CartPriceInCP <= 0 ? 0.97 : 1.03),
        r: Math.min(width, height) / 200
        
      }];
      modelYears = data.find(d => d.Territory === selectedIso).dataArr.filter(d => years.includes(d.year)) || [];
      // 这里||是或逻辑运算符。如果没有找到与当前选定的ISO国家相匹配的的数据对象,或者筛选后的数据为空,就将‘modelYears’设置为空数组‘[]’

    }

    // the three year highlighters
    select(container).selectAll('.year-circle')
      .data(modelYears)
      .join(enter => enter.append('circle')
                      .attr('class', 'year-circle')
                      .attr('fill', 'orange')
                      .attr('opacity', 0.6)
                      .attr('cx', 0)
                      .attr('cy', 0)
                      .attr('r', 0)
                      //初始时不显示
                      .call(enter => enter.transition().duration(100)
                      //对于新圈的元素 调用‘transition‘方法来创建一个过渡效果 持续时间为100ms
                        .attr('cx', d => Math.sin(Math.PI - scCountryAngle(selectedIso)) * scYearRadius(d.year))
                        .attr('cy', d => Math.cos(Math.PI - scCountryAngle(selectedIso)) * scYearRadius(d.year))
                        .attr('r', d => scMortRate(d.value))),
            update => update.transition().duration(100)
            //处理更新状态
                        .attr('cx', d => Math.sin(Math.PI - scCountryAngle(selectedIso)) * scYearRadius(d.year))
                        .attr('cy', d => Math.cos(Math.PI - scCountryAngle(selectedIso)) * scYearRadius(d.year))
                        .attr('r', d => scMortRate(d.value)),
            exit => exit.transition().duration(100)
            //退出状态
                      .attr('cx', 0)
                      .attr('cy', 0)
                      .attr('r', 0)
                      .remove()
      );

    // the CartPriceInCP highlighter
    select(container).selectAll('.CartPriceInCP-circle')
      .data(modelCartPriceInCP)
      .join(enter => enter.append('circle')
                      .attr('class', `CartPriceInCP-circle ${CartPriceInCP <= 0 ? 'decreased' : 'increased'}`)
                      // 根据减少率的正负值 为每个圆圈元素添加不同的类名 用于后续的样式设置
                      .attr('cx', 0)
                      .attr('cy', 0)
                      .attr('r', 0)
                      .call(enter => enter.transition().duration(100)
                        .attr('cx', d => d.cx)
                        .attr('cy', d => d.cy)
                        .attr('r', d => d.r)),
            update => update
                        .attr('class', `CartPriceInCP-circle ${CartPriceInCP <= 0 ? 'decreased' : 'increased'}`)
                        .transition().duration(100)
                          .attr('cx', d => d.cx)
                          .attr('cy', d => d.cy)
                          .attr('r', d => d.r),
            exit => exit.transition().duration(100)
                      .attr('cx', 0)
                      .attr('cy', 0)
                      .attr('r', 0)
                      .remove()
      );
  }

  $: if (container) update(selectedIso);
</script>

<g transform="translate({width / 2} {height / 2})" bind:this={container}></g>

<style>
  :global(circle.increased) {
    opacity: 1;
    fill: rgb(70, 130, 180);
  }
/* 这里将增加的死亡率用红色显示 透明度完全不透明*/

  :global(circle.decreased) {
    opacity: 1;
    fill: rgb(70, 130, 180);
  }
</style>
