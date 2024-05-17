<script>
  import { onMount } from 'svelte';

  export let width;
  export let height;
  export let offset;
  export let data;
  export let years;
  export let scYearColor;
  export let scCountryAngle;
  export let scYearRadius;
  export let scMortRate;
  export let selectedIso;

  const canvasScaleFactor = 2;

  // Elements
  let canvas, ctx;

  function init() {
    canvas.width = canvasScaleFactor * width;
    //设置 Canvas 元素的宽度和高度为原始宽度和高度
    //乘以缩放比例 canvasScaleFactor，以实现高分辨率绘图。
    canvas.height = canvasScaleFactor * height;
    canvas.style.width = `${width}px`;
    canvas.style.height = `${height}px`;
    //设置 Canvas 元素的样式宽度和高度为原始宽度和高度，以确保 Canvas 在页面上显示的大小和原始大小一致。
    canvas.style.margin = `${offset / 2}px`;

    ctx.scale(canvasScaleFactor, canvasScaleFactor);
    ctx.translate(width / 2, height / 2);
    //将绘图原点移动到 Canvas 元素的中心，以便后续的绘图操作基于 Canvas 元素的中心进行。

    ctx.globalCompositeOperation = 'luminosity';
    //设置绘图上下文的全局合成操作为 'luminosity'，这会使绘制的图形与已存在的图形进行混合，
    //具体效果根据所绘制图形和背景图形的亮度进行计算。
  }

  function drawSword(x, y, r) {
    ctx.beginPath();

    const handleWidth = r * 0.2; // Adjust handle width as needed
    const handleHeight = r * 0.4; // Adjust handle height as needed
    const bladeLength = r * 1.2; // Adjust blade length as needed
    const bladeWidth = r * 0.3; // Adjust blade width as needed

    // Handle top left
    ctx.moveTo(x - handleWidth / 2, y);

    // Handle bottom left
    ctx.lineTo(x - handleWidth / 2, y + handleHeight);

    // Blade bottom left
    ctx.lineTo(x - bladeWidth / 2, y + handleHeight + bladeLength * 0.2);

    // Blade bottom right
    ctx.lineTo(x + bladeWidth / 2, y + handleHeight + bladeLength * 0.2);

    // Blade top right
    ctx.lineTo(x + bladeWidth / 2, y + handleHeight + bladeLength);

    // Blade tip (adjust for a sharper point)
    ctx.lineTo(x, y + handleHeight + bladeLength + r * 0.1);

    // Handle top right
    ctx.lineTo(x + handleWidth / 2, y + handleHeight);

    // Handle top left (close the path)
    ctx.lineTo(x - handleWidth / 2, y);

    ctx.closePath();
    ctx.fillStyle = scYearColor(years);
    ctx.fill();
  }
  function drawShield(x, y, r) {
  ctx.beginPath();

  const notchRatio = 0.25; // Adjust notch ratio for shield indentation
  const notchDepth = r * notchRatio; // Notch depth based on radius
  const handleWidth = r * 0.15; // Adjust handle width
  const handleLength = r * 0.3; // Adjust handle length

  // Left side with notch
  ctx.moveTo(x - r, y);
  ctx.lineTo(x - r + notchDepth, y + notchDepth);
  ctx.lineTo(x - r + notchDepth, y + r - notchDepth);

  // Top curve
  const numPointsTop = 15; // Adjust for smoothness
  const angleStepTop = Math.PI / numPointsTop;
  for (let i = 0; i <= numPointsTop; i++) {
    const angle = i * angleStepTop;
    const bulge = Math.sin(angle) * r * 0.1; // Adjust bulge amount
    const cx = x - r + notchDepth + (r - 2 * notchDepth) * Math.cos(angle) + bulge;
    const cy = y + r - notchDepth - (r - 2 * notchDepth) * Math.sin(angle);
    if (i === 0) {
      ctx.lineTo(cx, cy);
    } else {
      ctx.lineTo(cx, cy);
    }
  }

  // Right side
  ctx.lineTo(x + r, y + notchDepth);
  ctx.lineTo(x + r, y);

  // Handle base
  ctx.lineTo(x + r - handleWidth / 2, y + handleLength);

  // Handle curve
  ctx.quadraticCurveTo(x, y + handleLength * 1.2, x - handleWidth / 2, y);

  ctx.closePath();
  ctx.fillStyle = scYearColor(years);
  ctx.fill();
}

function drawStar(ctx, x, y, radius, numPoints, innerRadius) {
    ctx.beginPath();
    for (let i = 0; i < 2 * numPoints; i++) {
        const r = i % 2 === 0 ? radius : innerRadius;
        const angle = (i * Math.PI) / numPoints;
        const xPos = x + Math.cos(angle) * r;
        const yPos = y + Math.sin(angle) * r;
        if (i === 0) {
            ctx.moveTo(xPos, yPos);
        } else {
            ctx.lineTo(xPos, yPos);
        }
    }
    ctx.closePath();
    ctx.fill();
}




  function draw(width, height, selectedIso) {
    ctx.clearRect(-width / 2, -height / 2, width, height);
    //这行代码用于清除 canvas 上的内容，以便重新绘制新的图形。
    //它使用 clearRect 方法清除一个矩形区域，
    //矩形的左上角位于 (-width / 2, -height / 2)，宽度为 width，高度为 height。
    ctx.globalAlpha = selectedIso ? 0.1 : 0.4;
    //这行代码根据 selectedIso 变量的值设置绘制的图形的不透明度。
    //如果 selectedIso 为真，则不透明度为 0.1，否则为 0.4。



    years.forEach(year => {
      ctx.fillStyle = scYearColor(year);
      data.forEach(d => {
        const yearData = d.dataArr.find(d => d.year === year);
        const x = Math.sin(Math.PI - scCountryAngle(d.Territory)) * scYearRadius(year);
        const y = Math.cos(Math.PI - scCountryAngle(d.Territory)) * scYearRadius(year);
        ctx.beginPath();
        //ctx.arc(x, y, scMortRate(yearData.value)*0.3, 0, 2 * Math.PI);
        drawStar(ctx,x, y,scMortRate(yearData.value) * 0.45,15,scMortRate(yearData.value) * 0.1); // Adjust radius as needed

        //在给定的坐标 (x, y) 处绘制一个圆弧，2*pi就是一整个圆形
        //圆心坐标为 (x, y)，半径由 scMortRate 函数根据数据值确定。
        ctx.fill();
      });
    });
  }

  onMount(() => {
    ctx = canvas.getContext('2d');
  });
  //这行代码在组件挂载到 DOM 上后立即执行。在这个函数内部，
  //它获取了 canvas 的 2D 渲染上下文（getContext('2d')），并将其赋值给了 ctx 变量。

  $: if (ctx) init(width, height);
  //这是一个响应式声明，它会在依赖的变量发生变化时重新运行。这里，它检查 ctx 是否存在，
  //如果存在，则调用 init 函数来初始化 canvas 的宽度、高度等参数


  $: if (ctx && data) draw(width, height, selectedIso);
  //同样是一个响应式声明，它在 ctx 和 data 变化时重新运行。
  //如果 ctx 和 data 都存在，它会调用 draw 函数来绘制图形。
</script>

<canvas class="canvas-visual"
        bind:this={canvas}></canvas>

<style>
  canvas {
    position: absolute;
  }
</style>
