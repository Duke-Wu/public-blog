# echarts 图表添加点击事件

## eCharts设置点击横轴事件

1. 设置是横轴事件还是竖轴事件：

   **横轴**

   ```
   xAxis: {
       triggerEvent: true
   }
   ```

   **竖轴**

   ```
   yAxis: {
       triggerEvent: true
   }
   ```

2. 添加点击事件

   ```
   myChart.on("click", function (param) { 
       console.log(param)
       alert(1)
   })
   ```

   