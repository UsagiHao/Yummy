<template>
  <el-row>
    <el-col :span="4">
      <el-menu
        default-active="1"
        @select="handleSelect">
        <el-menu-item index="1">
          <i class="el-icon-menu"></i>
          <span slot="title">我的订单</span>
        </el-menu-item>
        <el-submenu index="2">
          <template slot="title">
            <i class="el-icon-location"></i>
            <span>我的资料</span>
          </template>
          <el-menu-item-group>
            <el-menu-item index="2-1">个人资料</el-menu-item>
            <el-menu-item index="2-2">地址管理</el-menu-item>
          </el-menu-item-group>
        </el-submenu>
      </el-menu>
    </el-col>
    <el-col :span="20">
      <el-table
        :data="orderData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
        style="width: 100%"
       >
        <el-table-column
          prop="time"
          label="下单时间"
          width="180">
        </el-table-column>
        <el-table-column
          prop="content"
          label="订单内容"
          width="480">
          <template slot-scope="scope">
            <img :src="scope.row.image"  min-width="70" height="70" />
            <span>{{scope.row.content}}</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="price"
          label="支付金额（元）">
        </el-table-column>
        <el-table-column
          prop="state"
          label="状态">
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="getOrderDetail(scope.$index, scope.row)">订单详情
            </el-button>
            <br>
            <el-button
              size="mini"
              type="danger"
              @click="orderAgain(scope.$index, scope.row)">再来一份
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="text-align: center;margin-top: 30px;">
        <el-pagination
          background
          layout="prev, pager, next"
          :total="total"
          @current-change="current_change">
        </el-pagination>
      </div>
    </el-col>
  </el-row>
</template>

<script>
  export default {
    name: 'MyOrders',
    data: function () {
      return {
        tabPosition:'left',
        orderData: [
          {
            oid: 1,
            time: '2019-06-06 10:43',
            content: '\t\n' +
              '巨无霸单人套餐1份 1 个菜品',
            price: 24.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 2,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'

          },
          {
            oid: 3,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'

          },
          {
            oid: 4,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 5,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 6,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 7,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 8,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 9,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 10,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
          {
            oid: 11,
            time: '2019-06-05 10:43',
            content:'\t\n' +
              '爆浆蛋包饭+雪花鸡柳1份 1 个菜品',
            price: 18.9,
            state: '订单已完成',
            image : 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg'
          },
        ],
        total: 11,
        pagesize:10,
        currentPage:1
      }
    },

    methods:{
      current_change:function(currentPage){
        this.currentPage = currentPage;
      },

      handleSelect(key, keyPath) {
        console.log(key, keyPath);
        if (key === "1"){
          this.$router.push('/customer/home/MyOrders')}
        if (key === "2-1"){
          this.$router.push('/customer/home/UserInfo')
        }
        if (key === "2-2"){
          this.$router.push('/customer/home/AddressManage')
        }
      },

      //跳转至订单详情
      getOrderDetail(index, row){
        console.log(row);
        localStorage.setItem("order", row);
        this.$router.push('/customer/home/OrderDetail');
      },

      orderAgain(){
      },

      loadInfo: function () {
        this.$ajax({
          url: '/customer/orders/get',
          method: 'get'
        }).then(res => {
          if (res.data['AccessDenied']) {
            this.$router.push('/')
          } else {
            this.orderData = res.data;
          }
        })
      }
    }
  }
</script>

<style>

</style>
