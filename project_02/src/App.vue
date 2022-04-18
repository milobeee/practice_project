<template>
  <div class="app-container">
    <!-- header 头部区域 -->
    <Header title="购物车案例"></Header>
    <Goods
      v-for="item in list"
      :key="item.id"
      :id="item.id"
      :title="item.goods_name"
      :pic="item.goods_img"
      :price="item.goods_price"
      :state="item.goods_state"
      :count="item.goods_count"
      @state-change="getNewState"
    ></Goods>
    <Footer
      :isfull="fullState"
      :amount="amt"
      :allc="total"
      @full-change="getFullState"
    ></Footer>
  </div>
</template>
 
<script>
import axios from "axios";
import Header from "@/components/Header/Header.vue";
import Goods from "@/components/Goods/Goods.vue";
import Footer from "@/components/Footer/Footer.vue";
import bus from "@/components/eventBus.js";
export default {
  data() {
    return {
      //用来储存购物车列表数据，默认为空数组
      list: [],
    };
  },
  computed: {
    fullState() {
      return this.list.every((item) => item.goods_state === true);
    },
    amt() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce(
          (total, item) => (total += item.goods_price * item.goods_count),
          0
        );
    },
    total() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce((t, item) => (t += item.goods_count), 0);
    },
  },
  created() {
    //调用请求数据的方法
    this.initCartList();
    bus.$on("share", (val) => {
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_count = val.value;
          return true;
        }
      });
    });
  },
  methods: {
    //封装请求列表数据的方法
    async initCartList() {
      //调用 axios 的 get 方法，请求列表数据
      const { data: res } = await axios.get("https://www.escook.cn/api/cart");
      if (res.status === 200) {
        this.list = res.list;
      }
    },
    getNewState(val) {
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_state = val.value;
          return true;
        }
      });
    },
    getFullState(val) {
      this.list.forEach((item) => (item.goods_state = val));
    },
  },
  components: {
    Header,
    Goods,
    Footer,
  },
};
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
