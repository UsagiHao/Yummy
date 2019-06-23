<template>
  <div>
    <div style="background-color: #1E89E0">
      <el-row>
        <el-col :span="4" style="margin-top: 1%">
          <img :src="require('../../assets/logo.png')" width="140" height="70"/>
        </el-col>
        <el-col :span="4" style="margin-top: 1%; margin-left: -3%">
          <span style="color: white; font-size: 30px" >|订单信息</span>
        </el-col>
        <el-col :span="16" style="margin-top: 1%;"  >
          <el-steps :space="200" :active="2" align-center="" style="float: right;width: 60%;" >
            <el-step title="选择商品" ></el-step>
            <el-step title="确认订单信息"></el-step>
            <el-step title="成功提交订单"></el-step>
          </el-steps>
        </el-col>
      </el-row>
    </div>
    <div align="left" style="margin-left: 2%;">
      <el-row>
        <el-col :span="8">
          <el-card :body-style="{ padding: '3px'}" shadow="hover" style="width: 100%; height: 500px;padding-right: 5%">
            <div style="padding: 6px">
              <div>
                <span style="font-size:18px; margin-left: 5%; font-family: 微软雅黑;font-weight: bold">订单详情</span>
                <span style="font-size:12px; margin-left: 50%; font-family: 微软雅黑;">{{"\>"}}返回商家修改</span>
                <el-divider></el-divider>
                <div style="margin-left: 4%">
                  <el-table
                    :data="shoppingCartTableData"
                    style="width: 100%"
                  >
                    <el-table-column
                      prop="food"
                      label="商品"
                    >
                    </el-table-column>
                    <el-table-column
                      label="份数"
                      width="150"
                      align="center"
                    >
                      <template slot-scope="scope">
                      <span v-if="scope.row.food === '配送费' || scope.row.food === '餐盒'">
                      </span>
                        <el-input-number v-else size="mini" v-model="scope.row.number" :min="0" :max="100" label="描述文字" style="padding-right: 2%;" @change="changeNumber(scope.$index, scope.row)"></el-input-number>
                      </template>
                    </el-table-column>
                    <el-table-column
                      prop="price"
                      label="小计(元)"
                    align="right">
                    </el-table-column>
                  </el-table>
                </div>
                <el-row>
                  <span style="font-size: 40px; color: red; float: right">￥{{this.sumPrice}}</span>
                </el-row>
                <el-row>
                  <span style="margin-left: 80%; float: right">共{{this.sumNumber}}份商品</span>
                </el-row>
              </div>
              <div style="position: relative;top: 30px;">
              </div>
            </div>
          </el-card>
        </el-col>
        <el-col :span="16" style="padding-left: 1%;padding-right: 3%">
          <el-card :body-style="{ padding: '3px'}" shadow="hover" style="width: 100%; height: 600px;padding-right: 5%">
            <div style="padding: 6px">
              <div>
                <span style="font-size:18px; margin-left: 5%; font-family: 微软雅黑;font-weight: bold">收货地址</span>
                <span style="font-size:12px; float: right; font-family: 微软雅黑;">
                  <el-link type="primary" @click="dialogFormVisible = true">添加新地址</el-link>
                  <el-dialog title="添加新地址" :visible.sync="dialogFormVisible">
                    <el-form :model="form">
                      <el-form-item label="地址" :label-width="formLabelWidth">
                        <el-input v-model="form.address" autocomplete="off"></el-input>
                      </el-form-item>
                      <el-form-item label="手机号" :label-width="formLabelWidth">
                        <el-input v-model="form.tel" autocomplete="off"></el-input>
                      </el-form-item>
                    </el-form>
                    <div slot="footer" class="dialog-footer">
                      <el-button @click="cancelAddAddress()">取 消</el-button>
                      <el-button type="primary" @click="addAddress()">确 定</el-button>
                    </div>
                  </el-dialog>
                </span>
                <el-select v-model="value" placeholder="请选择收货地址" style="margin-top: 2%;margin-left: 5%;margin-right: 30%; width: 95%">
                  <el-option
                    v-for="item in addresses"
                    :key="item.address + item.tel"
                    :label="item.address"
                    :value="item.address">
                    <span style="float: left">{{ item.address }}</span>
                    <span style="float: right; color: #8492a6; font-size: 13px">{{ item.tel }}</span>
                  </el-option>
                </el-select>
                <br>
                <br>
                <span style="font-size:18px; margin-left: 5%; font-family: 微软雅黑;font-weight: bold;">
                  其他信息
                </span>
                <el-form ref="form" :model="orderForm" label-width="80px" style="margin-left: 5%; margin-top: 2%">
                  <el-form-item label="送达时间">
                    <el-time-select
                      v-model="arriveTime"
                      :picker-options="pickerOptions"
                      placeholder="选择时间">
                    </el-time-select>
                  </el-form-item>
                  <el-form-item label="订单备注">
                    <el-input v-model="orderForm.note" style="width: 300px"></el-input>
                  </el-form-item>
                </el-form>
                <span style="margin-left: 5%">
                  <el-button type="danger" @click="buy()" :loading = isLoading>{{this.buttonText}}</el-button>
                </span>
              </div>
              <div style="position: relative;top: 30px;">
              </div>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'CheckOut',
    data() {
      return {
        formLabelWidth: '120px',
        buttonText : '确认下单',
        isLoading : false,
        pickerOptions: {
          start: '08:30',
          step: '00:15',
          end: '18:30',
          disabledDate(time) {
            return time.getTime() > Date.now();
          }
        },
        arriveTime: '',
        addresses:[{address:"江苏省南京市鼓楼区汉口路22号南京大学鼓楼校区陶园2舍407", tel:"15189585960"},
          {address:"江苏省南京市鼓楼区汉口路22号南京大学鼓楼校区费彝民楼", tel:"13770752307"},
          {address:"江苏省南京市鼓楼区汉口路22号南京大学", tel:"15189585960"},
          {address:"江苏省淮安市清江浦区江苏省淮阴中学", tel:"15189585960"},
          {address:"江苏省淮安市经济开发区无锡路2号翔宇壹号5栋1102", tel:"15189585960"},],
        value: '',
        dialogFormVisible: false,
        form: {
          address:'',
          tel:''
        },
        orderForm: {
          note: ''
        },
        shoppingCartTableData:[],
        sumPrice:0,
        sumNumber:0
      }
    },

    mounted(){
      this.getSum();
    },

    methods:{
      async getSum(){
        await this.getShoppingCart();
        this.sumPrice = 0;
        this.sumNumber = 0;
        for (let i = 0; i < this.shoppingCartTableData.length; i++){
          this.sumPrice += this.shoppingCartTableData[i].number * this.shoppingCartTableData[i].singlePrice;
        }
        for (let i = 0; i < this.shoppingCartTableData.length; i++){
          if (this.shoppingCartTableData[i].food !== "配送费" &&  this.shoppingCartTableData[i].food !== "餐盒")
          this.sumNumber += this.shoppingCartTableData[i].number;
        }
      },

      changeNumber(index, row){
        if (row.number < this.shoppingCartTableData[index].number){
          this.shoppingCartTableData[index].number = row.number;
          this.sumPrice -= this.shoppingCartTableData[index].singlePrice;
          this.sumNumber --;
        }else {
          this.shoppingCartTableData[index].number = row.number;
          this.sumPrice += this.shoppingCartTableData[index].singlePrice;
          this.sumNumber ++;
        }
      },

      cancelAddAddress(){
        this.dialogFormVisible = false;
        this.form.tel = "";
        this.form.address = "";
      },

      addAddress(){
        this.dialogFormVisible = false;
        this.value = this.form.address;
        this.addresses.push({address:this.form.address, tel:this.form.tel});
        this.form.tel = "";
        this.form.address = "";
      },

      buy(){
        this.isLoading = true;
        this.buttonText = '下单中';
        this.$router.push('/customer/CheckStand');
      },

      async getShoppingCart(){
        let order = {
          rid: 'xxxxxxxxxx',
          cartItems: {
            'xxx': {name: 'abc', price: 20.23, num: 1}, // key是菜品编号，value是数量
            'xxxx': {name: 'bbc', price: 20.23, num: 1},
          },
          cost: {
            before: 56.28, //优惠前
            after: 31.28 // 优惠后
          }
        };
        console.log(JSON.stringify(order));
        console.log(JSON.parse(JSON.stringify(order)))

        order = JSON.parse(JSON.stringify(order))

       /* let order = localStorage.getItem("orderInfo");
        order = JSON.parse(order);*/
        let before = order.cost.before;
        let after = order.cost.after;
        let cut = before - after;
        let cartItems = order.cartItems;
        console.log(cartItems);
        let table = [];
        Object.keys(cartItems).forEach(function(key){
          let name = cartItems[key].name;
          let singlePrice = cartItems[key].price;
          let num = cartItems[key].num;
          let price = num * singlePrice;
          price = "￥" + price;
          table.push({food: name, number: num, price: price, singlePrice: singlePrice});
        });
        console.log(table)
        this.shoppingCartTableData = table;
      }
    }
  }
</script>

<style scoped>

</style>
