<template>
  <div class="type-nav">
    <div class="container">
      <div @mouseleave="leaveshow" @mouseenter="entershow">
        <h2 class="all">全部商品分类</h2>
        <!-- 过渡动画 -->
        <transition name="sort">
          <div class="sort" v-show="data.show" @mouseleave="changeCur">
            <div class="all-sort-list2" @click="goSearchs">
              <!-- 一级分类 -->
              <div
                class="item"
                v-for="(c1, index) in store.dataList"
                :key="c1.categoryId"
                :class="{ cur: currentIndex === index }"
              >
                <h3 @mouseenter="changeIndex(index)">
                  <a
                    :data-categoryName="c1.categoryName"
                    :data-category1Id="c1.categoryId"
                    >{{ c1.categoryName }}</a
                  >
                </h3>
                <div class="item-list clearfix">
                  <!-- 二级分类 -->
                  <div
                    class="subitem"
                    v-for="c2 in c1.categoryChild"
                    :key="c2.categoryId"
                  >
                    <dl class="fore">
                      <dt>
                        <a
                          :data-categoryName="c2.categoryName"
                          :data-category2Id="c2.categoryId"
                          >{{ c2.categoryName }}}</a
                        >
                      </dt>
                      <dd>
                        <!-- 三级分类 -->
                        <em v-for="c3 in c2.categoryChild" :key="c3.categoryId">
                          <a
                            :data-categoryName="c3.categoryName"
                            :data-category3Id="c3.categoryId"
                            >{{ c3.categoryName }}</a
                          >
                        </em>
                      </dd>
                    </dl>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </transition>
      </div>
      <nav class="nav">
        <a href="###">服装城</a>
        <a href="###">美妆馆</a>
        <a href="###">尚品汇超市</a>
        <a href="###">全球购</a>
        <a href="###">闪购</a>
        <a href="###">团购</a>
        <a href="###">有趣</a>
        <a href="###">秒杀</a>
      </nav>
    </div>
  </div>
</template>


<script>
import { defineComponent} from "vue";
export default defineComponent({
  name: "TypeNav",
});
</script>

<script setup >
import useStoreData from "@/store/dataTypeNav";
import { useRouter, useRoute} from "vue-router";
import { onMounted, reactive, ref } from "@vue/runtime-core";
import _ from "lodash";


const store = useStoreData();
const router = useRouter()
const route = useRoute()

let currentIndex = ref(-1);
// 由于search路径有keywork占位符，其他路由跳转过去时候，也需要给这个占位符赋值才行。

let data = reactive({
  keyword: "0",
  show: "true",
});

// 鼠标移入修改样式，并实现了节流
const changeIndex = _.throttle(function (index) {
  currentIndex.value = index;
}, 20);

// 鼠标移出修改样式
function changeCur() {
  currentIndex.value = -1;
}

// 点击三级联动的a标签，跳转到search组件
function goSearchs(event) {
  let element = event.target;
  //节点的dataset属性，可以获取节点的自定义属性和属性值。如果当前节点有data-categoryname属性，则它就是a标签。
  let { categoryname, category1id, category2id, category3id } = element.dataset;
  if (categoryname) {
    //整理路由跳转的参数
    let location = { name: "search" };
    let query = { categoryName: categoryname };
    if (category1id) {
      query.category1Id = category1id;
    } else if (category2id) {
      query.category2Id = category2id;
    } else {
      query.category3Id = category3id;
    }
    location.query = query;
    if(route.params){
      location.params = route.params
    }
    router.push(location);
  }
}


// 鼠标移入typenav时，菜单显示
function entershow() {
  if (router.currentRoute.value.path != "/home") {
    data.show = true;
  }
}

// 鼠标移入typenav时，菜单隐藏
function leaveshow() {
  if (router.currentRoute.value.path != "/home") {
    data.show = false;
  }
}

// 请求三级联动的数据
onMounted(() => {
  store.getTypeNavData();
  // 组件挂载时，路由路径不是首页，则菜单隐藏
  if (router.currentRoute.value.path != "/home") {
    data.show = false;
  }
});
</script>

<style scoped lang="less">
.type-nav {
  border-bottom: 2px solid #e1251b;

  .container {
    width: 1200px;
    margin: 0 auto;
    display: flex;
    position: relative;

    .all {
      width: 210px;
      height: 45px;
      background-color: #e1251b;
      line-height: 45px;
      text-align: center;
      color: #fff;
      font-size: 14px;
      font-weight: bold;
    }

    .nav {
      a {
        height: 45px;
        margin: 0 22px;
        line-height: 45px;
        font-size: 16px;
        color: #333;
      }
    }

    .sort {
      position: absolute;
      left: 0;
      top: 45px;
      width: 210px;
      height: 461px;
      position: absolute;
      background: #fafafa;
      z-index: 999;

      .all-sort-list2 {
        .item {
          h3 {
            line-height: 30px;
            font-size: 14px;
            font-weight: 400;
            overflow: hidden;
            padding: 0 20px;
            margin: 0;

            a {
              color: #333;
            }
          }

          .item-list {
            display: none;
            position: absolute;
            width: 734px;
            min-height: 460px;
            background: #f7f7f7;
            left: 210px;
            border: 1px solid #ddd;
            top: 0;
            z-index: 9999 !important;

            .subitem {
              float: left;
              width: 650px;
              padding: 0 4px 0 8px;

              dl {
                border-top: 1px solid #eee;
                padding: 6px 0;
                overflow: hidden;
                zoom: 1;

                &.fore {
                  border-top: 0;
                }

                dt {
                  float: left;
                  width: 54px;
                  line-height: 22px;
                  text-align: right;
                  padding: 3px 6px 0 0;
                  font-weight: 700;
                }

                dd {
                  float: left;
                  width: 415px;
                  padding: 3px 0 0;
                  overflow: hidden;

                  em {
                    float: left;
                    height: 14px;
                    line-height: 14px;
                    padding: 0 8px;
                    margin-top: 5px;
                    border-left: 1px solid #ccc;
                  }
                }
              }
            }
          }

          &:hover {
            .item-list {
              display: block;
            }
          }
        }

        .cur {
          background: skyblue;
        }
      }
    }
    // 过渡动画的样式
    //过渡动画开始状态（进入）
    .sort-enter {
      height: 0px;
    }
    //过渡动画结束状态（进入）
    .sort-enter-to {
      height: 461px;
    }
    //定义动画时间、速率
    .sort-enter-active {
      transition: all 0.5s linear;
    }
  }
}
</style>