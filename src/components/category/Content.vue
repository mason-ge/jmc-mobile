<template>
  <div class="wrap">
    <div class="wrapper" ref="wrapper">
      <div class="content">
        <div class="content-sec" v-show="showFlg">
          <span v-for="(tab ,index) in secCatgList" :class="{cur:isActive==index}" :key="index" @click="isActive=index,tabChange(tab.enumvName)">{{tab.enumvName}}</span>
        </div>
        <div class="divBox">
          <div class="groupBox">
            <scroller :on-refresh="refresh" :on-infinite="infinite" ref="myscroller" class="file-lists" no-data-text="没有更多商品哦">
              <ul>
                <li v-for="(value,index) in prodList" :key="index" @click="handleClick(value,index)">
                  <img v-bind:src="value.img" :alt="value.prodName" />
                  <span class="text">{{value.prodName}}</span>
                </li>
              </ul>
            </scroller>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import BScroll from "better-scroll";
import { Toast } from "mint-ui";
//import myAxios from '../../util/myAxios.js';
export default {
  name: "Content",
  data() {
    return {
      prodList: [],
      pageNo: 0,
      secCatgList: [],
      isActive: 0, //当前选择 样式
      nowFirstCatg: "所有品类",
      nowSecCatg: "",
      pageNo: 1,
      pageSize: 10,
      client: "0001",
      isLastPage: false,
      showFlg: false
    };
  },
  components: {},
  computed: {},
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
      var _self = this;
      var postData = {
        pageNo: _self.pageNo,
        pageSize: _self.pageSize,
        client: _self.client,
        firstCatg: _self.nowFirstCatg,
        secCatg: _self.nowSecCatg
      };

      _self.axios
        .post(
          "http://yun.jmcsolution.cn:8080/JmcScm/rest/prod/getProdList",
          postData
        )
        .then(function(respone) {
          _self.isLastPage = respone.data.data.isLastPage;
          // 判断是下拉刷新还是上拉加载（这一步也是比较巧妙的，当然也很好理解）
          if (_self.pageNo == 1) {
            _self.prodList = respone.data.data;
          } else {
            _self.prodList = _self.prodList.concat(respone.data.data);
          }
        })
        .catch(function(erro) {
          console.log(erro);
        });
    },
    // 下拉刷新
    refresh() {
       alert("下拉刷新");
      var _self = this;
      _self.pageNo = 1; //重置页数刷新每次页数都是第一页
      _self.isLastPage = false; //重置数据判断
      setTimeout(
        function() {
          _self.showData();
          _self.$refs.myscroller.finishPullToRefresh(); //刷新完毕关闭刷新的转圈圈
        }.bind(_self),
        1700
      );
    },
    // 上拉加载
    infinite(done) {
       alert("上拉加载");
      var _self = this;
      _self.$refs.myscroller.finishInfinite(true); //这个方法是不让它加载了，显示“没有更多数据”，要不然会一直转圈圈
      // if (_self.isLastPage) {
      //   _self.$refs.myscroller.finishInfinite(true); //这个方法是不让它加载了，显示“没有更多数据”，要不然会一直转圈圈
      // } else {
      //   setTimeout(() => {
      //     _self.pageNo++; //下拉一次页数+1
      //     _self.showData();
      //     done(); //进行下一次加载操作
      //   }, 1500);
      // }
    },
    //父级组件的点击触发的方法
    refreshSecCatg(firstCatg) {
      var _self = this;
      _self.pageNo = 1;
      _self.prodList = [];
      _self.secCatgList = [];
      //讲当前的品类一级传给全局变量
      _self.nowFirstCatg = firstCatg;
      if (firstCatg === "所有品类") {
        _self.showFlg = false;
        _self.showData();
      } else {
        _self.showFlg = true;
        var postData = {
          firstDesc: firstCatg
        };
        _self.axios
          .post(
            "http://yun.jmcsolution.cn:8080/JmcScm/rest/prod/getSecCatgByFirstCode",
            postData
          )
          .then(function(respone) {
            _self.secCatgList = respone.data.data;
          })
          .catch(function(erro) {
            console.log(erro);
          });
      }
    },
    //选择二级品类刷新
    tabChange(secCatg) {
      var _self = this;
      _self.nowSecCatg = secCatg;
      _self.showData();
    },
    mounted() {
      this.$nextTick(() => {
        this._initScroll();
        this.contentScroll.scrollTo(0, 1, 1, "easing");
        this.contentScroll.scrollTo(0, 0, 1, "easing");
      });
    }
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
      height: 100%;
      padding: 1.1rem 0.1rem;
      box-sizing: border-box;

      .content-sec {
        font-size: 0;
        height: 25px;
        line-height: 25px;
        //display: none;
      }
      .content-sec > span {
        cursor: pointer;
        display: inline-block;
        font-size: 16px;
        text-align: center;
        width: 100px;
      }
      .cur {
        color: #fff;
        background-color: #20a0ff;
      }

      .divBox {
        position: relative;
        width: 100%;
        height: 100%;
        .titleImg {
          width: 100%;
        }
        .groupBox {
          position: relative;
          width: 100%;
          height: 100%;
          //margin-top: 1.2rem;
          // .childTitle {
          //   font-size: 0.2rem;
          //   color: #575757;
          //   margin-bottom: 5px;
          // }
          .file-lists {
            height: 100%;
            ul {
              position: relative;
              width: 100%;
              box-sizing: border-box;
              //padding: 5px;
              display: flex;
              background: white;
              flex-wrap: wrap;
              li {
                width: 50%;
                box-sizing: border-box;
                //border-right: 1px solid #e0e0e0;
                //border-top: 1px solid #e0e0e0;
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
                  width: 90%;
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>