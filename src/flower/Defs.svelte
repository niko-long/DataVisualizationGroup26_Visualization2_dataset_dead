<script>
  export let scCartPriceInCP;
</script>

<defs>
  <!-- gradient for the CartPriceInCP path -->
  <radialGradient id="CartPriceInCP-gradient"
                  gradientUnits="userSpaceOnUse"
                  cx="0"
                  cy="0"
                  r={scCartPriceInCP.range()[1]}>
    <stop offset="0"
          stop-color="rgba(200,200,255,0.3)"></stop>
    <stop offset={scCartPriceInCP(0) / scCartPriceInCP.range()[1]}
          stop-color="rgba(50, 94, 147,0.4)"></stop>
    <stop offset={scCartPriceInCP(0) / scCartPriceInCP.range()[1]}
          stop-color="rgb(0, 44, 97,0.6)"></stop>
    <stop offset="1"
          stop-color="rgb(0, 44, 97)"></stop>
  </radialGradient>

  <!-- blur filter for the outer circle -->
  <!--定义滤镜效果 给外圈添加阴影效果-->
  <filter id="CartPriceInCP-shadow">
    <feDropShadow dx="0"
                  dy="0"
                  stdDeviation="100"
                  flood-color="#A6D9F7"></feDropShadow>
  </filter>

  <filter id="turbulence">
      <feTurbulence type="fractalNoise" baseFrequency="0.05" numOctaves="3" result="turbulence"/>
      <feDisplacementMap in="SourceGraphic" in2="turbulence" scale="10"/>
  </filter>

  <filter id="backgroundTexture">
      <feTurbulence type="fractalNoise" baseFrequency="0.01" numOctaves="2" result="turbulence"/>
      <feDisplacementMap in="SourceGraphic" in2="turbulence" scale="5"/>
    </filter>
    

    
</defs>

<style>
</style>
