<script>
  import Defs from './Defs.svelte';
  import Continents from './Continents.svelte';
  import CartPriceInCPPath from './CartPriceInCP.svelte';

  export let width;
  export let height;
  export let offset;
  export let data;
  export let years;
  export let scCountryAngle;
  export let scYearRadius;
  export let scCartPriceInCP;
  export let scDeliveryTimeCost;
  console.log('scDeliveryTimeCost:', scDeliveryTimeCost);

  let areasData;

  function loadAreasData() {
    const uniqueAreas = [...new Set(data.map(d => d.Area))];
    // data.map(d => d.area) 获取数据集中所有area的列表
    // [...new Set()] 去除了重复的area名称，保留了唯一的area列表。
    //通过 map 方法遍历唯一的area列表，对每个area进行处理，
    //生成一个包含每个area起始角度、结束角度和area名称的对象。
    areasData = uniqueAreas.map(Area => {
      const raw = data.map(d => d.Area);
      //首先，通过 data.map(d => d.Area) 获取数据集中所有大陆的列表，
      //存储在 raw 变量中。
      return {
        startAngle: scCountryAngle(data[raw.indexOf(Area)].Territory),
        //然后，通过 indexOf() 方法获取当前大陆在 raw 列表中第一次出现的索引，
        //并通过 lastIndexOf() 方法获取当前大陆在 raw 列表中最后一次出现的索引
        endAngle: scCountryAngle(data[raw.lastIndexOf(Area)].Territory),
        //使用这两个索引分别获取对应大陆的 ISO 编码，
        //并将 ISO 编码传递给 scCountryAngle() 函数，以获取该国家在极坐标中的角度
        //将获取到的起始角度和结束角度分别存储到 startAngle 和 endAngle 属性中。
        Area
        //在 map 方法中遍历唯一的大陆列表，对每个大陆执行以下操作：
        //首先，通过 data.map(d => d.continent) 获取数据集中所有大陆的列表，
        //并找到当前大陆在列表中第一次出现的索引和最后一次出现的索引，
        //分别使用 indexOf() 和 lastIndexOf() 方法
      };
    });
  }

  // Prepare data for continent labels
  $: if (data) loadAreasData();
  //这段代码是 Svelte 中的响应式声明（Reactive declaration）。
  //当 data 变量的值改变时，将触发 loadContinentsData() 函数的执行。
</script>

<svg class="svg-visual"
     width={width}
     height={height}
     style="margin: {offset / 2}px;">
  <!--<Defs scCartPriceInCP={scCartPriceInCP} />-->
  <CartPriceInCPPath width={width}
                 height={height}
                 data={data}
                 scCountryAngle={scCountryAngle}
                 scCartPriceInCP={scCartPriceInCP}
                 scDeliveryTimeCost={scDeliveryTimeCost} />
  <Continents width={width}
              height={height}
              data={areasData}
              years={years}
              scYearRadius={scYearRadius} />
</svg>

<style>
  svg {
    position: absolute;
    
  }
</style>
