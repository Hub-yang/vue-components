<script setup lang="ts">
import echarts from '@/components/BaseEcharts/config'
const xData = ['五星级', '四星级', '三星级', '二星及一星级', '非星级']
const data = [30, 20, 60, 50, 80]
const lineData = [0.2, 0.4, 0.3, 0.7, 0.5]
const max = 100
const MAX = Array.from({ length: data.length }, () => max)
// 绘制左侧面
const CubeLeft = echarts.graphic.extendShape({
  shape: {
    x: 0,
    y: 0
  },
  buildPath: function (ctx, shape) {
    // 会canvas的应该都能看得懂，shape是从custom传入的
    const xAxisPoint = shape.xAxisPoint
    const c0 = [shape.x, shape.y]
    const c1 = [shape.x - 6, shape.y - 4]
    const c2 = [xAxisPoint[0] - 6, xAxisPoint[1] - 4]
    const c3 = [xAxisPoint[0], xAxisPoint[1]]
    ctx.moveTo(c0[0], c0[1])?.lineTo(c1[0], c1[1]).lineTo(c2[0], c2[1]).lineTo(c3[0], c3[1]).closePath()
  }
})

// 绘制右侧面
const CubeRight = echarts.graphic.extendShape({
  shape: {
    x: 0,
    y: 0
  },
  buildPath: function (ctx, shape) {
    const xAxisPoint = shape.xAxisPoint
    const c1 = [shape.x, shape.y]
    const c2 = [xAxisPoint[0], xAxisPoint[1]]
    const c3 = [xAxisPoint[0] + 6, xAxisPoint[1] - 4]
    const c4 = [shape.x + 6, shape.y - 4]
    ctx.moveTo(c1[0], c1[1])?.lineTo(c2[0], c2[1]).lineTo(c3[0], c3[1]).lineTo(c4[0], c4[1]).closePath()
  }
})

// 绘制顶面
const CubeTop = echarts.graphic.extendShape({
  shape: {
    x: 0,
    y: 0
  },
  buildPath: function (ctx, shape) {
    const c1 = [shape.x, shape.y]
    const c2 = [shape.x + 6, shape.y - 4]
    const c3 = [shape.x + 0, shape.y - 8]
    const c4 = [shape.x - 6, shape.y - 4]
    ctx.moveTo(c1[0], c1[1])?.lineTo(c2[0], c2[1]).lineTo(c3[0], c3[1]).lineTo(c4[0], c4[1]).closePath()
  }
})
// 注册三个面图形
echarts.graphic.registerShape('CubeLeft', CubeLeft)
echarts.graphic.registerShape('CubeRight', CubeRight)
echarts.graphic.registerShape('CubeTop', CubeTop)

const option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    },
    textStyle: {
      fontSize: 12
    },
    formatter: '{a2}:{c2}</br>{a3}:{c3}'
  },
  legend: {
    x: '330',
    y: '25',
    icon: 'none',
    itemWidth: 16,
    itemHeight: 5,
    textStyle: {
      fontSize: 12,
      color: '#B6D3EB',
      fontWeight: 400
    },
    data: ['房价同比增长率', '房价']
  },
  xAxis: {
    type: 'category',
    data: xData,
    axisLine: {
      lineStyle: {
        color: '#3A5D7A'
      }
    },
    axisTick: {
      show: false
    },
    axisLabel: {
      color: '#B6D3EB',
      fontSize: 12,
      interval: 0
    }
  },
  yAxis: [
    {
      min: 0,
      max,
      interval: 20,
      type: 'value',
      splitLine: {
        lineStyle: {
          type: 'dashed',
          color: 'rgba(58,93,122,0.5)'
        }
      },
      axisLine: {
        show: false
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        color: '#B6D3EB',
        fontSize: 12
      }
    },
    {
      min: 0,
      max: 1,
      type: 'value',
      splitLine: {
        lineStyle: {
          type: 'dashed',
          color: 'rgba(58,93,122,0.5)'
        }
      },
      axisLine: {
        show: false
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        color: '#B6D3EB',
        fontSize: 12,
        formatter: function (value: any) {
          return value * 100 + '%'
        }
      }
    }
  ],
  series: [
    {
      type: 'custom',
      renderItem: function (_: any, api: any) {
        const location = api.coord([api.value(0), api.value(1)])
        return {
          type: 'group',
          children: [
            {
              type: 'CubeLeft',
              shape: {
                api,
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: 'rgba(47,102,192,.27)'
              }
            },
            {
              type: 'CubeRight',
              shape: {
                api,
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: 'rgba(59,128,226,.27)'
              }
            },
            {
              type: 'CubeTop',
              shape: {
                api,
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: 'rgba(72,156,221,.27)'
              }
            }
          ]
        }
      },
      data: MAX
    },
    {
      type: 'custom',
      renderItem: (_: any, api: any) => {
        const location = api.coord([api.value(0), api.value(1)])
        var color = new echarts.graphic.LinearGradient(0, 0, 0, 1, [
          {
            offset: 0,
            color: 'rgba(37,170,254,1)'
          },
          {
            offset: 0.8,
            color: 'rgba(37,170,254,0.1)'
          }
        ])
        return {
          type: 'group',
          children: [
            {
              type: 'CubeLeft',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: color
              }
            },
            {
              type: 'CubeRight',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: color
              }
            },
            {
              type: 'CubeTop',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: color
              }
            }
          ]
        }
      },
      data
    },
    {
      type: 'bar',
      name: '房价',
      itemStyle: {
        color: 'transparent'
      },
      data: MAX
    },
    {
      type: 'line',
      name: '房价同比增长率',
      yAxisIndex: 1,
      showAllSymbol: true,
      symbol: 'circle',
      symbolSize: 1,
      itemStyle: {
        color: '#fff',
        shadowColor: '#FFE003',
        shadowBlur: 20,
        borderColor: '#FFE003',
        borderWidth: 5
      },
      lineStyle: {
        width: 1,
        color: '#FFE003'
      },
      data: lineData
    }
  ]
}
</script>

<template>
  <BaseEchart width="300px" height="300px" :option="option" />
</template>
