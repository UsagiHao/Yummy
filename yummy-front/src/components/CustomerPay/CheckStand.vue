<template>
  <div>
    <div style="background-color: #1E89E0">
      <el-row>
        <el-col span="4" style="margin-top: 1%">
          <img :src="require('../../assets/logo.png')" width="140" height="70"/>
        </el-col>
        <el-col span="4" style="margin-top: 1%; margin-left: -3%">
          <span style="color: white; font-size: 30px" >|收银台</span>
        </el-col>
        <el-col span="16" style="margin-top: 1%;"  >
          <el-steps :space="200" :active="3" align-center="" style="float: right;width: 60%">
            <el-step title="选择商品"></el-step>
            <el-step title="确认订单信息"></el-step>
            <el-step title="成功提交订单"></el-step>
          </el-steps>
        </el-col>
      </el-row>
    </div>
    <div align="left" style="margin-left: 2%;">
      <el-card :body-style="{ padding: '3px'}" shadow="hover" style="width: 100%;padding-right: 5%; margin-top: 0%">
        <div style="padding: 6px">
          <div>
            <div style="font-size:18px; margin-left: 5%; font-family: 微软雅黑;font-weight: bold">
              订单详情
            </div>
            <el-collapse v-model="activeNames" @change="handleChange" style="margin-left: 5%">
              <el-collapse-item title="外卖订单" name="1">
                <div>{{this.restaurantName}}</div>
                <div>{{this.username}}</div>
                <div>{{this.tel}}</div>
                <div>{{this.address}}</div>
              </el-collapse-item>
            </el-collapse>
          </div>
        </div>
      </el-card>
      <el-card :body-style="{ padding: '3px'}" shadow="hover" style="width: 100%;padding-right: 5%; margin-top: 1%">
        <div style="padding: 6px">
          <div>
            <div style="font-size:18px; margin-left: 5%; font-family: 微软雅黑;font-weight: bold">
              请选择支付方式
              <el-divider></el-divider>
              <div>
                <el-radio v-model="radio1" label="1" border>支付宝</el-radio>
                <el-radio v-model="radio1" label="2" border>微信</el-radio>
              </div>
              <el-divider></el-divider>
              <el-button type="danger" @click="buy()" :loading = isLoading>确认支付</el-button>
            </div>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'CheckStand',
    data() {
      return {
        restaurantName:'',
        username:'',
        tel:'',
        address:'',
        foods:[],
        radio1:0
      }
    },

    created(){
      this.getOrder();
    },

    methods:{
      getOrder(){
        this.restaurantName = "麦当劳（中山大厦店）";
        this.username = "蔡徐坤";
        this.tel = "15189585960";
        this.address = "江苏省南京市鼓楼区南京大学"
      },
    }
  }
</script>

<style scoped>

</style>
