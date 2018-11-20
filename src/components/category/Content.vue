<template>
  <div class="wrap">
    <div class="wrapper" ref="wrapper">
      <div class="content">
        <!-- <div v-for="(secCatg,index) in secCatgList" :key="index" class="divBox"> -->
        <!--<img v-if="v.topImg" class="titleImg" :src="v.topImg" :alt="v.title" />-->
        <!-- <p secCatg-else>{{secCatg.enumvName}}</p>
					<div v-for="(val,index) in v.list" :key="index" class="groupBox"> -->
        <!-- <p class="childTitle">{{val.p_title}}</p> -->
        <!-- <ul>
							<li v-for="(prod,index) in prodList" :key= index>
								<img :src="prod.img" :alt="prod.prodName" />
							</li>
						</ul>
					</div>
				</div> -->
        <li v-for="(secCatg,index) in secCatgList" :key=index>
          <p secCatg-else>{{secCatg.enumvName}}</p>
        </li>
        <li v-for="(prod,index) in prodList" :key=index>
          <img :src="prod.img" :alt="prod.prodName" />
        </li>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import BScroll from "better-scroll";
import { Toast } from "mint-ui";
import qs from "qs";
//import myAxios from '../../util/myAxios.js';
export default {
  name: "Content",
  props: ["showWho"],
  data() {
    return {
      prodList: [],
      pageNo: 0,
      contentScroll: null,
      secCatgList: []
    };
  },
  computed: {
    ...mapGetters(["categoryData"]),
    currentData: function() {
      if (this.showWho) {
        //第一次会undefind
        return this.categoryData.data.cateData.filter(
          v => v.cate_shouji_id == this.showWho.id
        );
      }
    }
  },
  created() {
    //var _self = this;
    //this.showData();
  },
  methods: {
    _initScroll() {
      this.contentScroll = new BScroll(this.$refs.wrapper, {
        bounce: {
          top: false,
          bottom: false,
          left: false,
          right: false
        },
        click: true
      });
    },
    handleClick({ name }) {
      Toast({
        message: name,
        position: "top",
        duration: 1000,
        className: "myToast"
      });
    },
    //分页查询并显示数据
    showData() {
      alert(11);
      var postData = {
        pageNo: 1,
        pageSize: 15,
        client: "0001"
      };
      this.axios
        .post(
          "http://yun.jmcsolution.cn:8080/JmcScm/rest/prod/getProdList",
          postData
        )
        .then(function(respone) {
          this.prodList = respone.data.data;
        })
        .catch(function(erro) {});
    },
    //父级组件的点击触发的方法
    refreshSecCatg(firstCatg) {
      var postData = {
        firstDesc: firstCatg
      };
      this.axios
        .post(
          "http://yun.jmcsolution.cn:8080/JmcScm/rest/prod/getSecCatgByFirstCode",
          postData
        )
        .then(function(respone) {
          console.log(respone);
          this.secCatgList = respone.data.data;
        })
        .catch(function(erro) {});
    }
  },
  mounted() {
    this.$nextTick(() => {
      this._initScroll();
      this.contentScroll.scrollTo(0, 1, 1, "easing");
      this.contentScroll.scrollTo(0, 0, 1, "easing");
    });
  }
};
</script>

<style lang="less" scoped>
.wrap {
  position: relative;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  .wrapper {
    position: relative;
    height: 100%;
    overflow: hidden;
    .content {
      position: relative;
      width: 100%;
      padding: 1.2rem 0.2rem;
      box-sizing: border-box;
      .divBox {
        position: relative;
        width: 100%;
        .titleImg {
          width: 100%;
        }
        .groupBox {
          position: relative;
          width: 100%;
          margin-top: 0.2rem;
          .childTitle {
            font-size: 0.2rem;
            color: #575757;
            margin-bottom: 5px;
          }
          ul {
            position: relative;
            width: 100%;
            box-sizing: border-box;
            padding: 5px;
            display: flex;
            background: white;
            flex-wrap: wrap;
            li {
              width: 33.3%;
              box-sizing: border-box;
              border-right: 1px solid #e0e0e0;
              border-top: 1px solid #e0e0e0;
              padding: 5px;
              text-align: center;
              &:nth-child(3n) {
                border-right: none;
                border-right: 0px solid #e0e0e0;
              }
              &:nth-child(1) {
                border-top: none;
                border-top: 0px solid #e0e0e0;
              }
              &:nth-child(2) {
                border-top: none;
                border-top: 0px solid #e0e0e0;
              }
              &:nth-child(3) {
                border-top: none;
                border-top: 0px solid #e0e0e0;
              }
              img {
                width: 80%;
              }
            }
          }
        }
      }
    }
  }
}
</style>