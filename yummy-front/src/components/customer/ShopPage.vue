<template>
  <v-flex>
    <v-layout xs12 class="shop-header" align-center>
      <v-flex md1></v-flex>
      <!----------------- Shop Name & Address---------------------->
      <v-img
        :src="restaurant.avatarSrc"
        max-width="100"
        max-height="100"
        class="shop-avatar"
      >
      </v-img>
      <v-flex shrink mx-3>
        <h2 style="color: white; text-align: start">{{restaurant.name}}</h2>
        <h4 style="color: white; text-align: start">{{restaurant.address}}</h4>
        <v-layout justify-start>
          <template v-for="i in Array(Math.floor(restaurant.stars)).keys()">
            <v-icon :key="i" color="yellow">star</v-icon>
          </template>
          <v-icon v-if="restaurant.stars > Math.floor(restaurant.stars)" color="yellow">
            star_half
          </v-icon>
        </v-layout>
      </v-flex>
      <v-flex md1>
      </v-flex>
      <!------- 起送价、配送费 ------->
      <v-flex>
        <v-layout justify-end>
          <v-flex md2>
            <h3 style="color: white">起送价</h3>
            <h2 style="color: white">{{restaurant.priceStart}}元</h2>
          </v-flex>
          <v-flex md2>
            <h3 style="color: white">配送费</h3>
            <h2 style="color: white">{{restaurant.priceDelivery}}元</h2>
          </v-flex>
          <v-flex md2></v-flex>
        </v-layout>
      </v-flex>
    </v-layout>
    <!--------------- Menu and Shopping Cart --------------->
    <v-layout xs12>
      <!---------------- Meal Type List ----------------->
      <v-flex ml-3>
        <v-card elevation="5" class="sticky-box">
          <v-list class="type-list" dense>
            <template v-for="type in restaurant.types">
              <v-list-tile
                :key="type"
                :class="currentType === type ? 'type-selected': ''"
                @click="afterTypeSelected(type)"
              >
                <v-list-tile-content>
                  <v-list-tile-title class="text-xs-right">{{type}}</v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
          </v-list>
        </v-card>
      </v-flex>
      <!---------------- Meal List ----------------->
      <v-flex sm6 mx-5 my-3>
        <template v-for="type in restaurant.types">
          <v-layout :key="'title-' + type" mt-3 mb-1>
            <h2 :id="'h-' + type">{{type}}</h2>
          </v-layout>
          <v-layout :key="type" wrap>
            <template v-for="meal in restaurant.menu[type]">
              <v-flex :key="meal.id" my-2>
                <v-card class="meal-card" width="320">
                  <v-layout align-center>
                    <!------------- Meal Image ------------->
                    <v-img
                      :src="meal.picSrc"
                      width="100"
                      height="100"
                      max-width="100"
                      max-height="100"
                    ></v-img>
                    <!------------ Meal Info --------------->
                    <v-flex ml-3 mr-1 my-2>
                      <v-tooltip top>
                        <h3 class="text-sm-left" slot="activator">
                          <b>{{compressStr(meal.name, 10)}}</b>
                        </h3>
                        <span>{{meal.name}}</span>
                      </v-tooltip>
                      <v-tooltip v-if="meal['desc'].length > 0" top>
                        <h4 class="text-sm-left" slot="activator">
                          {{compressStr(meal['desc'], 10)}}
                        </h4>
                        <span>{{meal['desc']}}</span>
                      </v-tooltip>
                      <v-tooltip v-else top>
                        <h4 slot="activator">&ensp;</h4>
                        <span>无菜品描述</span>
                      </v-tooltip>
                      <v-layout justify-space-between align-content-end>
                        <h3 class="text-sm-left" style="color: red"><b>￥{{meal.price.toFixed(2)}}</b></h3>
                        <!---------------- Number Ordered ----------------->
                        <div
                          v-if="cartItems[meal.id] !== undefined"
                          style="margin: 0 10px"
                        >
                          <NumInput
                            v-model="cartItems[meal.id]"
                            @input="clearIfZero(cartItems, meal.id)"
                          ></NumInput>
                        </div>
                        <v-btn v-else flat icon small style="margin: 0 10px"
                               @click="$set(cartItems, meal.id, 1)"
                        >
                          <v-icon color="success">add_circle</v-icon>
                        </v-btn>
                      </v-layout>
                    </v-flex>
                  </v-layout>
                </v-card>
              </v-flex>
            </template>
          </v-layout>
        </template>
      </v-flex>
      <!---------------- Shopping Cart ----------------->
      <v-flex sm3>
        <v-card class="cart-card">
          <!----------------- Cart Item List ----------------------->
          <v-layout class="cart-item-list">
            <v-flex>
              <v-list dense>
                <v-list-tile>
                  <v-layout justify-space-between align-center>
                    <v-flex shrink>
                      <h3><b>我的购物车</b></h3>
                    </v-flex>
                    <v-flex shrink>
                      <v-btn flat small @click="clearCart">
                        <v-icon color="red">delete_forever</v-icon>
                        清空
                      </v-btn>
                    </v-flex>
                  </v-layout>
                </v-list-tile>
                <template v-for="(num, mid) in cartItems">
                  <v-divider :key="'d-' + mid"></v-divider>
                  <v-list-tile :key="mid">
                    <v-list-tile-content>
                      <v-layout justify-space-between style="width: 200px">
                        <v-flex grow align-self-center>
                          <h4>{{compressStr(restaurant.mealDict[mid].name, 8)}}</h4>
                        </v-flex>
                        <v-flex shrink align-self-center>
                          <NumInput
                            v-model="cartItems[mid]"
                            @input="clearIfZero(cartItems, restaurant.mealDict[mid].id)"
                          ></NumInput>
                        </v-flex>
                      </v-layout>
                    </v-list-tile-content>
                    <v-list-tile-action>
                      <h3>￥{{(restaurant.mealDict[mid].price * num).toFixed(2)}}</h3>
                    </v-list-tile-action>
                  </v-list-tile>
                </template>
              </v-list>
            </v-flex>
          </v-layout>
          <!----------------- Discount Area ----------------------->
          <v-layout justify-center class="promotion-area">
            <template v-for="(discount, amount, index) in restaurant.promotions">
              <span :key="amount">满{{amount}}减{{discount}}</span>
              <span v-if="index < Object.keys(restaurant.promotions).length - 1" :key="index">，</span>
            </template>
          </v-layout>
          <!----------------- Action Area ----------------------->
          <v-layout>
            <v-btn dark class="cart-btn cart-btn-left">
              <v-flex>
                <v-layout justify-start px-3 align-center>
                  <v-badge overlap color="red">
                    <span v-if="cartItemCount > 0" slot="badge">
                      {{cartItemCount}}
                    </span>
                    <v-icon>shopping_cart</v-icon>
                  </v-badge>
                  <template v-if="cost.after > 0">
                    <h4>￥</h4>
                    <h2><b>{{cost.after.toFixed(2)}}</b></h2>
                    <h3>&nbsp;|</h3>
                  </template>
                  <h4 v-if="cost.before > cost.after">
                    <s>￥{{cost.before.toFixed(2)}}</s>
                  </h4>
                </v-layout>
              </v-flex>
              <v-flex shrink pr-3>
                <h5 v-if="restaurant.priceDelivery > 0">配送费<br>￥{{restaurant.priceDelivery.toFixed(2)}}</h5>
                <h5 v-else>免配送费</h5>
              </v-flex>
            </v-btn>
            <v-flex v-if="cost.after < restaurant.priceStart" style="background-color: #b6b8bf">
              <v-layout style="height: 50px" align-center justify-center>
                <h4><b>差{{(restaurant.priceStart - cost.after).toFixed(2)}}元起送</b></h4>
              </v-layout>
            </v-flex>
            <v-btn v-else class="cart-btn cart-btn-right" dark color="success" @click="gotoPlace">
              <h3><b>去结算 ></b></h3>
            </v-btn>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>
    <!------------- Back to Top Button ------------->
    <v-btn v-if="backTop" large fixed icon right dark color="success" style="top: 50%"
           @click="scrollToTop"
    >
      <v-icon>arrow_upward</v-icon>
    </v-btn>
  </v-flex>
</template>

<script>
import NumInput from '@/components/util/NumInput'
export default {
  name: 'ShopPage',
  components: {NumInput},
  data: function () {
    return {
      restaurant: {
        id: this.$route.params['rid'],
        name: '',
        address: '',
        avatarSrc: '',
        priceStart: 0,
        priceDelivery: 0,
        stars: 0,
        promotions: {
          35: 10,
          40: 15,
          50: 20
        },
        types: [],
        menu: {},
        mealDict: {}
      },
      currentType: '',
      scrolling: false,
      cartItems: {},
      backTop: false
    }
  },
  computed: {
    cartItemCount: function () {
      let count = 0
      for (let c of Object.keys(this.cartItems)) {
        count += this.cartItems[c]
      }
      return count
    },
    cost: function () {
      let costBefore = 0
      for (let mid of Object.keys(this.cartItems)) {
        costBefore += this.restaurant.mealDict[mid].price * this.cartItems[mid]
      }
      let costLevel = 0
      for (let amount of Object.keys(this.restaurant.promotions)) {
        if (costBefore >= amount) {
          costLevel = amount
        } else {
          break
        }
      }
      let costAfter = costBefore
      if (costLevel > 0) {
        costAfter -= this.restaurant.promotions[costLevel]
      }
      return {
        before: costBefore,
        after: costAfter
      }
    }
  },
  beforeMount: function () {
    // load restaurant info
    this.$ajax({
      url: '/restaurant/get',
      method: 'get',
      params: {
        'rid': this.restaurant.id
      }
    }).then(res => {
      console.log(res)
      let r = this.restaurant
      r.name = res.data.data['name']
      r.priceStart = res.data.data['priceStart']
      r.priceDelivery = res.data.data['priceDelivery']
      r.avatarSrc = this.preprocessImgSrc(res.data.data['avatarSrc'])
      r.address = res.data.data['address']
      r.description = res.data.data['description']
      r.stars = res.data.data['stars']
    })
    // load meals
    this.$ajax({
      url: '/restaurant/get/meals',
      method: 'get',
      params: {
        'rid': this.restaurant.id
      }
    }).then(res => {
      console.log(res)
      /**
       * response form: [
       * {
       *    id: 'meal id',
       *    name: 'meal name',
       *    desc: 'meal description',
       *    price: 'meal price',
       *    type: 'meal type',
       *    picSrc: 'meal picture src'
       * }
       * ]
       */
      this.processMeals(res.data.data)
    })
  },
  mounted: function () {
    window.onscroll = () => {
      this.updateCurrentType()
      this.backTop = document.body.getBoundingClientRect().y < -200
    }
  },
  methods: {
    afterTypeSelected: function (type) {
      this.scrolling = true
      // scroll to the corresponding header
      let target = document.getElementById('h-' + type)
      let offset = 65
      window.scrollTo({
        top: target.offsetTop - offset,
        left: window.screenX,
        behavior: 'smooth'
      })
      this.scrolling = false
    },
    scrollToTop: function () {
      this.scrolling = true
      window.scrollTo({
        top: 0,
        left: 0,
        behavior: 'smooth'
      })
      this.scrolling = false
    },
    compressStr: function (name, limit) {
      if (name.length > limit) {
        return name.substr(0, limit - 2) + '...'
      } else {
        return name
      }
    },
    clearIfZero: function (items, mid) {
      if (items[mid] === 0) {
        this.$delete(this.cartItems, mid)
      }
    },
    clearCart: function () {
      for (let mid of Object.keys(this.cartItems)) {
        this.$delete(this.cartItems, mid)
      }
    },
    preprocessImgSrc: function (src) {
      let prefix = 'https://fuss10.elemecdn.com/'
      if (src.substring(src.length - 3) === 'png') {
        return prefix + src + '.png'
      } else {
        return prefix + src + '.jpeg'
      }
    },
    /**
     * Preprocess meals to initialize restaurant[types, menu, mealDict]
     * and currentType.
     */
    processMeals: function (meals) {
      for (let m of meals) {
        m['picSrc'] = this.preprocessImgSrc(m['picSrc'])
        this.$set(this.restaurant.mealDict, m['id'], m)
        if (m['type'] in this.restaurant.menu) {
          this.restaurant.menu[m['type']].push(m)
        } else {
          this.restaurant.types.push(m['type'])
          this.$set(this.restaurant.menu, m['type'], [m])
        }
      }
      if (this.restaurant.types.length > 0) {
        this.currentType = this.restaurant.types[0]
      }
    },
    /**
     * Update current type upon window scrolling.
     */
    updateCurrentType: function () {
      if (this.scrolling) {
        return
      }
      let offset = 70
      let lastType
      for (let type of this.restaurant.types) {
        let h = document.getElementById('h-' + type)
        if (h.getBoundingClientRect().y < offset) {
          lastType = type
        } else if (lastType) {
          this.currentType = lastType
          return
        }
      }
    },
    gotoPlace: function () {
      // TODO save cart items and leave the page
    }
  }
}
</script>
<style scoped>
  .shop-header {
    background: url('http://shadow.elemecdn.com/faas/desktop/media/img/shop-bg.0391dd.jpg');
    height: 120px;
    width: 100%
  }
  .shop-avatar {
    border-radius: 50%;
    border: 4px solid rgba(255, 255, 255, .3);
  }
  .sticky-box {
    position: -webkit-sticky;
    position: sticky;
    top: 60px;
    bottom: 0;
  }
  .type-list {
    /*background-color: #ede8ef;*/
  }
  .meal-card {
    /*background-color: #ede8ef;*/
  }
  .type-selected {
    background-color: #b0b6be;
  }
  .cart-card {
    position: fixed;
    bottom: 10px;
    right: 10px;
    width: 350px;
  }
  .cart-item-list {
    border-top: 2px solid #2070ef;
    border-bottom: #898b92 1px solid;
  }
  .promotion-area {
    background-color: antiquewhite;
  }
  .cart-btn {
    margin: 0 0 0 0;
    height: 50px;
    border-radius: 0 0 0 0;
  }
  .cart-btn-left {
    width: 230px;
  }
  .cart-btn-right {
    width: 120px;
  }
</style>
