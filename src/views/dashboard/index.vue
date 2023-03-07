<template>
  <div class="dashboard-container bg">
    <!--    头部标题-->
    <div class="module-box">
      <div style="flex: 0 1 30%;">
        <dv-decoration-10 style="height:5px;" />
      </div>
      <div style="flex: 0 1 40%;">
        <!--        三等分-->
        <div class="d-flex">
          <dv-decoration-8 style="height:50px;flex: 1" />
          <div style="flex: 1"><dv-decoration-11 style="height:50px;font-weight: bold;font-size: 20px;">诈骗案例分析与统计</dv-decoration-11></div>
          <dv-decoration-8 :reverse="true" style="height:50px;flex: 1" />
        </div>
      </div>
      <div style="flex: 0 1 30%;">
        <!--        旋转180° transform:rotate(180deg);-->
        <dv-decoration-10 style="height:5px;transform:rotate(180deg);" />
      </div>
    </div>
    <!--    一行两列-->
    <div class="module-box">
      <div style="flex: 0 1 45%;">
        <dv-border-box-11 title="全国分布图" style="width: 100%;height: 550px">
          <ve-map :data="chartData" :settings="chartSettings" height="510px" style="width: 85%; padding-top: 50px;margin: 0 auto" :legend-visible="false" />
        </dv-border-box-11>
        <dv-border-box-9 style="width: 100%;height: 270px">
          <!--          <p style="padding: 20px 0 0 30px;font-weight: bolder">最新诈骗案例</p>-->
          <!--          <dv-scroll-board :config="config2" style="width:90%;height:200px;margin: 0 auto;padding-top: 15px" />-->
          <ve-histogram :data="chartData3" :extend="chartExtend3" height="300px" style="width: 90%;margin: 0 auto;padding-top: 20px" :legend-visible="true" />
        </dv-border-box-9>
      </div>
      <div style="flex: 0 1 55%;">
        <dv-border-box-10 style="width: 100%;height: 450px;margin-top: 30px">
          <ve-pie :data="chartData2" :settings="chartSettings2" :extend="chartExtend2" style="padding-top: 30px;color: #ffffff" />
        </dv-border-box-10>
        <dv-border-box-1 style="width: 100%;height: 330px;margin-top: 10px">
          <ve-line :data="chartData1" :extend="chartExtend" height="330px" color="#ffffff" :legend-visible="false" style="width: 90%;margin: 0 auto;padding-top: 25px" />
        </dv-border-box-1>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  },
  data() {
    this.chartSettings = {
      labelMap: {
        'quantity': '诈骗数' // 修改指标名称
      },
      itemStyle: {
        normal: { // 不选中样式
          borderColor: '#49C1E3',
          areaColor: '#224F92' // 背景颜色
        },
        emphasis: { // 选中样式
          areaColor: '#113EBB'
        }
      },
      label: { // 文本设置
        show: true,
        fontSize: 8,
        fontWeight: 300,
        color: '#ffffff'
      }
    }
    this.chartExtend = {
      'xAxis.0.axisLabel.color': 'white', // x轴文本颜色
      'yAxis.0.axisLabel.color': 'white', // y轴文本改变颜色
      legend: {
        textStyle: {
          color: 'white'
        }
      }
    }
    this.chartExtend2 = {
      legend: {
        textStyle: {
          color: 'white'
        }
      }
    }
    this.chartExtend3 = {
      'xAxis.0.axisLabel.color': 'white', // x轴文本颜色
      'yAxis.0.axisLabel.color': 'white', // y轴文本改变颜色
      legend: {
        textStyle: {
          color: 'white'
        }
      },
      // x轴的文字倾斜
      'xAxis.0.axisLabel.rotate': 45,
      yAxis: {
        // 是否显示y轴线条
        axisLine: {
          show: true
        },
        // 纵坐标网格线设置，同理横坐标
        splitLine: {
          show: false
        },
        // 线条位置
        position: 'left',
        textStyle: {
          color: 'white'
        }
      },
      xAxis: {
        axisLine: {
          show: true
        },
        textStyle: {
          color: 'white'
        }
      },
      series: {
        label: { show: true, position: 'top' }
      }
      // 数据过多时出现横向滚动条
      // dataZoom: [
      //   {
      //     show: true,
      //     realtime: true,
      //     start: 0,
      //     end: 50
      //   },
      //   {
      //     type: 'inside',
      //     realtime: true,
      //     start: 0,
      //     end: 50
      //   }
      // ]
    }
    return {
      chartData: {
        columns: ['位置', '诈骗数'],
        rows: [
          { '位置': '吉林', '诈骗数': 123 },
          { '位置': '北京', '诈骗数': 1223 },
          { '位置': '上海', '诈骗数': 2123 },
          { '位置': '浙江', '诈骗数': 4123 }
        ]
      },
      chartData1: {
        columns: ['日期', '年诈骗数'],
        rows: [
          { 日期: '2012', 年诈骗数: 1393 },
          { 日期: '2014', 年诈骗数: 3530 },
          { 日期: '2016', 年诈骗数: 2923 },
          { 日期: '2018', 年诈骗数: 1723 },
          { 日期: '2020', 年诈骗数: 3792 },
          { 日期: '2022', 年诈骗数: 4593 }
        ]
      },
      chartSettings2: {
        type: 'pie'
      },
      chartData2: {
        columns: ['key', 'value'],
        rows: [
          { key: '冒充‘公检法’办案诈骗', value: 1393 },
          { key: '网络游戏虚假交易诈骗', value: 3530 },
          { key: '假客代购诈骗', value: 2923 },
          { key: '冒充熟人诈骗', value: 1723 },
          { key: '校园贷诈骗', value: 3792 },
          { key: '其它', value: 4593 }
        ]
      },
      config1: {
        data: [
          {
            name: '广东',
            value: 26734
          },
          {
            name: '河南',
            value: 14783
          },
          {
            name: '山东',
            value: 12893
          },
          {
            name: '四川',
            value: 9348
          },
          {
            name: '福建',
            value: 8994
          }
        ]
      },
      chartData3: {
        columns: ['data', 'number'],
        rows: [
          { data: '<18', number: 1393 },
          { data: '18-35', number: 3530 },
          { data: '36-45', number: 2923 },
          { data: '46-60', number: 1723 },
          { data: '>60', number: 1723 }
        ]
      }
    }
  }
}
</script>

<style lang="scss" scoped>
*{
  margin: 0;
  padding: 0;
}

html {
  -webkit-text-size-adjust: 100%;
  -ms-text-size-adjust: 100%;
  font-size: 62.5%;
}
body, input,button, a, textarea, select {
  margin: 0;
  font-family: "Helvetica Neue","Microsoft YaHei",Arial,serif;
  font-size: 1.4rem;
  line-height: 1.5;
  color: #333;
}

html, body {
  width: 100%;
  height: 100%;
  margin: 0;
}
a{
  text-decoration: none;
}

a:hover,a:focus {
  text-decoration: none;
}
a:hover {
  filter:alpha(opacity=90);
  opacity:.9;
}
//文本对齐
.tc{
  text-align: center;
}
.tr{
  text-align: right;
}
.tl{
  text-align: left;
}
//主体
.dashboard-container{
  color: #ddd;
  background: #000000;
  width: 100%;
  height: calc(100vh - 55px);
}
.bg{
  padding: 0;
  background: url("../../assets/img/bg.jpg") no-repeat center center;
  background-size: cover;
}
.module-box{
  display: flex;
  justify-content: space-between;   // 两端对齐
  margin: 5px;
}
.d-flex{
  display: flex;
}
</style>
