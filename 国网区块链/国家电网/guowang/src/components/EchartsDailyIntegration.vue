<!--
	时间：2017-05-02
	描述：积分查询
-->
<template>
  <div class="contract">
    <div class="cMe_title">
      <div class="cleft">
        <div class="left"></div>
        <span class="l"></span>
      </div>
      <span class="title">每日积分</span>
      <div class="cright">
        <span class="r"></span>
        <div class="right"></div>
      </div>
    </div>
    <span class="r-text" id="xx" @click="faDianLiang">积分明细查询</span>
    <div class="hello">
      <div id="main1" style="width: 100%;height:400px;"></div>
    </div>
  </div>
</template>

<script>
  import echarts from 'echarts';
  export default {
    name: 'hello',
    data() {
      return {
        // 时间
        parameterDate:{
          newDate:'',
        },
        // 传参时间
        parameterData:{
          startDate:'',
          endDate:'',
        },
        xAxisData:[],
        seriesData:[],
      }
    },
    methods: {
      // 第一次加载请求数据，展示echarts
      getEeacher(obj){
        this.$api.post('Integral/getByMonth',obj,(res)=>{
          if(res.body.month.split(',').length <=1){
            this.parameterData.endDate = this.xAxisData[0];
          }else {
            this.xAxisData = res.body.month.split(',');
          }
          if(res.body.month.split(',').length <=1){
            this.parameterData.startDate = this.xAxisData[this.xAxisData.length-1];
          }else{
            this.seriesData = res.body.num.split(',');
          }
          this.draw();
        },function (res) {
        });
      },
      // 发电量明细
      faDianLiang(){
        this.$router.push({path:'/Header/ElectricityDetails',query:{isShowMeau:1,searchSign:1}});
      },
      // 获取当前时间日期
      getDate(){
        let Dates = new Date();
        let str = Dates.getFullYear()+"-"+(Dates.getMonth()+1)+"-"+Dates.getDate();
        this.parameterDate.newDate = str;
      },
      getOption() {
        let _this = this;
        return {
          graphic: [{
            id: 'left-btn',
            type: 'image',
            style: {
              image: this.$api.rootImage+'/map.png',
              width: 20,
              height: 260,
              opacity: 1
            },
            left:159,
            bottom:64,
            onclick: function() {
              _this.parameterData.endDate = _this.xAxisData[0];
              _this.getEeacher(_this.parameterData);
            }
          }, {
            id: 'right-btn',
            type: 'image',
            style: {
              image: this.$api.rootImage+'/map.png',
              width: 20,
              height: 260,
              opacity: 1
            },
            bottom:64,
            right: 134,
            onclick: function() {
              _this.parameterData.startDate = _this.xAxisData[_this.xAxisData.length-1];
              _this.getEeacher(_this.parameterData);
            }
          }],
          series: []
        };
      },
      draw() {
        let that = this;
        that.chart = echarts.init(document.getElementById('main1'));
        that.chart.setOption(that.getOption());
        that.chart.setOption({
          tooltip : {
            trigger: 'axis',
            formatter: "{a}为 : {c}千瓦",
            axisPointer: {
              type: 'cross',
              label: {
                backgroundColor: '#6a7985'
              }
            }
          },
          legend: {
            data: ['今日积分']
          },

          grid: {
            bottom: '12%',
            containLabel: true
          },
          xAxis: [
            {
              type: 'category',
              axisLabel: {
                interval: 0,
                rotate: 60
              },
              data:this.xAxisData
            }],
          yAxis: [{
            name:'千瓦',
            type: 'value'
          }],
          series: [{
            name: '今日积分',
            type: 'line',
            label: {
              normal: {
                show: true,
                position: 'top'
              }
            },
            itemStyle: {
              normal: {
                color:'#0aaf7e'
              }
            },
            areaStyle: {
              normal: {}
            },
            data: that.seriesData
          }]
        })
      }
    },
    mounted() {
      let that = this;
      this.getDate();
      this.getEeacher(this.parameterData);
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .xian{

  }
  .contract{
    padding-bottom: 30px;
    background-color: #f3f9f9;
  }
  .cMe_content{
    width: 1200px;
    margin: 0 auto;
  }
  .cMe_title{
    height:25px;
    clear: both;
    width: 1200px;
    margin: 0 auto;
    margin-bottom: 30px;
  }
  .cleft,.cright{
    display: inline-block;
  }
  .left,.right{
    display: inline-block;
    width:488px;
    height: 2px;
  }
  .left{
    float:left ;
    background-image:linear-gradient(to right,#FFF,#0BB07E);
  }
  .right{
    float:right ;
    background-image:linear-gradient(to left,#FFF,#0BB07E);
  }
  .l,.r{
    width: 4px;
    height:4px;
    border-radius: 50%;
    background-color:#0BB07E ;
    margin-top: -1px;
  }
  .l{
    float:right ;
  }
  .r{
    float:left ;
  }
  .title{
    font-size: 25px;
    margin:0 40px;
  }
  .r-text{display:block;
    padding: 5px 10px;
    display: flex;
    justify-content: flex-end;
    /*background-color: #02676F;*/
    color: #02676F;
    /*float: right;*/
    margin-right: 10%;
    font-size: 12px;
  }
</style>
