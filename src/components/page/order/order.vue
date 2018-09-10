<template>
   <div>
       <div>dingda</div>
        <ve-line :data="chartData"></ve-line>
        <div class="pie">
            <ve-pie  :data="chartData1" :settings="chartSettings"></ve-pie>
        </div>
         <div class="pie" >
            <ve-pie :data="chartData1" :settings="chartSettings"></ve-pie>
        </div>
         <div class="pie" >
            <ve-pie :data="chartData1" :settings="chartSettings"></ve-pie>
        </div>
      
   </div>
</template>

<script>
import VeLine from 'v-charts/lib/line'
import VePie from 'v-charts/lib/pie'

export default {

  data() {
       this.chartSettings = {
        roseType: 'radius'
      }
    return {
        
      chartData: {
        columns: ["日期", "访问用户", "下单用户", "下单率"],
        rows: [
          { 日期: "1/1", 访问用户: 11393, 下单用户: 1093, 下单率: 122},
          { 日期: "1/2", 访问用户: 3530, 下单用户: 3230, 下单率:333 },
          { 日期: "1/3", 访问用户: 2923, 下单用户: 2623, 下单率: 11},
          { 日期: "1/4", 访问用户: 1723, 下单用户: 1423, 下单率:22 },
          { 日期: "1/5", 访问用户: 3792, 下单用户: 3492, 下单率: 33 },
          { 日期: "1/6", 访问用户: 4593, 下单用户: 4293, 下单率:44 }
        ]
      },
       chartData1: {
          columns: ['日期', '访问用户'],
          rows: [
            { '日期': '1/1', '访问用户': 1393 },
            { '日期': '1/2', '访问用户': 3530 },
            { '日期': '1/3', '访问用户': 2923 },
            { '日期': '1/4', '访问用户': 1723 },
            { '日期': '1/5', '访问用户': 3792 },
            { '日期': '1/6', '访问用户': 4593 }
          ]
        }
    };
  },
  components: { VeLine,VePie }
};
</script>

<style scoped>
.pie{
display: inline-block;
width: 400px;

}
</style>
