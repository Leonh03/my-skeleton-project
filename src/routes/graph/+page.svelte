<script>
    import { Chart } from 'svelte-echarts'
  
    let pricelist = [170,
        170,
        173,
        176,
        267,
        307,
        335,
        339,
        348,
        361,
        440,
        516,
        516,
        538,
        553,
        585,
        585,
        589,
        594,
        611,
        695,
        764,
        849,
        934,
        934,
        970,
        1019,
        1019,
        1048,
        1188,
        1188,
        1273,
        1435]

    const options = {
    color: '#0FBA81',
    animation: true,
    colorStops: [{
      offset: 0, color: 'red' // color at 0%
     }, {
      offset: 1, color: 'blue' // color at 100%
        }],
      xAxis: {
        data: Array.from({ length: pricelist.length }, (_, i) => i + 1),
        type: 'category',
        show: false
      },
      yAxis: {
        type: 'value',
        show: false
      },
      series: [
        {
          data: pricelist,
          type: 'line'
        },
      ],
    }
  </script>
  
  <div class="app">
    <Chart {options} />
  </div>
  
  <style>
    .app {
      width: 100vw;
      height: 100vh;
    }
  </style>