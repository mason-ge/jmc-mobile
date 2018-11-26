<template>
  <div class="category">
    <v-categoryheader></v-categoryheader>
    <div class="wrapper" ref="wrapper">
      <ul class="content" ref="content">
        <li v-for="(catg,index) in catgList" @click="handleSelect(catg,index)" :class="{'active':isActive==index}" :key="index">{{catg.enumvName}}</li>
      </ul>
    </div>
    <div class="right">
      <v-content ref="mychild"></v-content>
    </div>

  </div>
</template>

<script>
import Header from "../common/Header";
import Content from "./Content";
import BScroll from "better-scroll";
import { mapGetters } from "vuex";
export default {
  name: "",
  components: {
    "v-categoryheader": Header,
    "v-content": Content
  },
  data() {
    return {
      scroll: null,
      isActive: 0, //当前选择 样式
      catgList: [] //一级品类列表
    };
  },
  computed: {
    ...mapGetters(["categoryData"])
    // showWho() {
    //   return this.categoryData.data.cateList[this.isActive];
    // }
  },
  created() {
    this.showCatg();
  },
  methods: {
    handleSelect(catg, index) {
      if (this.isActive != index) {
        this.isActive = parseInt(index);
      }
      this.$refs.mychild.refreshSecCatg(catg.enumvCode);
    },
    _initScroll() {
      this.scroll = new BScroll(this.$refs.wrapper, {
        bounce: {
          top: false,
          bottom: false,
          left: false,
          right: false
        },
        click: true
      });
    },
    //分页查询并显示数据
    showCatg: function() {
      var _self = this;
      this.axios
        .get(
          "http://yun.jmcsolution.cn:8080/JmcScm/rest/sys/getEnumListByCode/PLYJ"
        )
        .then(function(respone) {
          _self.catgList = respone.data.data;
        })
        .catch(function(erro) {});
    }
  },

  mounted() {
    this.$nextTick(() => {
      this._initScroll();
    });
  }
};
</script>

<style lang="less" scoped>
.category {
  position: relative;
  font-size: 0.26rem;
  width: 100%;
  height: 100%;
  background: #f4f4f4;
  display: flex;
  .wrapper {
    position: relative;
    width: 1.2rem;
    height: 100%;
    overflow: hidden;
    .content {
      position: relative;
      width: 100%;
      background: white;
      padding-top: 0.9rem;
      padding-bottom: 1.1rem;
      li {
        width: 100%;
        height: 1rem;
        line-height: 1rem;
        text-align: center;
        box-sizing: border-box;
        border-bottom: 1px solid #eaeaea;
        border-right: 1px solid #eaeaea;
        &:last-child {
          border-bottom: none;
          border-bottom: 0px solid #eaeaea;
        }
        &.active {
          background: #f4f4f4;
          color: red;
          border-right: none;
          border-right: 0 solid #eaeaea;
        }
      }
    }
  }
  .right {
    position: relative;
    flex: 1;
    height: 100%;
    background: #f4f4f4;
  }
}
</style>