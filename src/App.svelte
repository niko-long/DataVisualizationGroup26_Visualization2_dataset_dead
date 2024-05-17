<script>
  import { csv } from 'd3-fetch';
  import Flower from './flower/Flower.svelte';

  const years = [2019, 2021, 2023];

  let data;

  async function load() {
    data = await csv('deadOrders.csv', d => { // /childhood-mortality
      //在public文件夹的文件好像可以直接引用?
      const dataArr = [];
      const dataArr2 = [];
      const returnObj = {
        //iso: d.iso,
        //country: d.country,
       //reduction: +d.reduction, //+号可以将csv的字符串形式的文件转换成数字类型
        //continent: d.continent,
        Territory: d.Territory,
        CartPriceInCP: +d.CartPriceInCP, // 将csv字符串的形式的文件转换成数字类型
        DeliveryTimeCost:+d.DeliveryTimeCost,
        AveSpending: +d.AveSpending,
        AccountNum: +d.AccountNum,
        Area: d.Area,
        Product:d.Product,
        Quantities:+d.Quantities
      };

      for (let key in d) {
        // 匹配列名，例如 'CartPriceInCP_2019'
        let match = key.match(/^CartPriceInCP_(\d{4})$/);
        if (match) {
          let year = +match[1]; // 提取年份
          dataArr.push({
            year: year,
            value: +d[key]
          });
        }
      }


      for (let key in d) {
        // 匹配列名，例如 'DeliveryTimeCost_2019'
        let match = key.match(/^DeliveryTimeCost_(\d{4})$/);
        if (match) {
          let year = +match[1]; // 提取年份
          dataArr2.push({
            year: year,
            value: +d[key]
          });
        }
      }

      returnObj['dataArr'] = dataArr;
      returnObj['dataArr2'] = dataArr2;
      return returnObj;
    });
  }

  load();//调用 load() 函数，启动了加载数据的过程。
</script>

<div class="wrapper">
  <div class="header">
    <h1>The Sales of Different Area</h1>
  </div>
  <div id="visual">
    {#if data}
      <Flower {data} {years} /> 
      <!-- 这里是判断语句 如果有数据则渲染Flower-->
    {/if}
  </div>
</div>

<style>
  .wrapper {
    width: 95%;
    height: 100%;
    margin: 0 auto;
  }

  .header {
    width: 100%;
    margin: 1.5rem 0;
    color: var(--medieval-yellow);
  }

  .header h1 {
    font-family: 'MedievalSharp', serif;
    font-weight: normal;
    font-size: calc(6rem + 7px);
    text-align: center;
  }

  #visual {
    position: relative;
    width: 100%;
    height: 100vmin;
  }
</style>
