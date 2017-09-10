<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width: 100%">
              <el-table-column prop="goodsName" label="商品名称" class-name="goodsName"></el-table-column>
              <el-table-column prop="count" label="数量"></el-table-column>
              <el-table-column prop="price" label="金额"></el-table-column>
              <el-table-column prop="caozuo" label="操作">
                <template scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>
              {{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
              <small>金额：</small>
              {{totalMoney}}
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGood">删除</el-button>
              <el-button type="success" @click="checkOut">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>

      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="good-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class='cookList'>
                  <li v-for="goods0 in type0Goods" @click="addOrderList(goods0)">
                    <span class="foodImg"><img :src="goods0.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods0.goodsName}}</span>
                    <span class="foodPrice">￥{{goods0.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class='cookList'>
                  <li v-for="goods1 in type1Goods" @click="addOrderList(goods1)">
                    <span class="foodImg"><img :src="goods1.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods1.goodsName}}</span>
                    <span class="foodPrice">￥{{goods1.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class='cookList'>
                  <li v-for="goods2 in type2Goods" @click="addOrderList(goods2)">
                    <span class="foodImg"><img :src="goods2.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods2.goodsName}}</span>
                    <span class="foodPrice">￥{{goods2.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class='cookList'>
                  <li v-for="goods3 in type3Goods" @click="addOrderList(goods3)">
                    <span class="foodImg"><img :src="goods3.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods3.goodsName}}</span>
                    <span class="foodPrice">￥{{goods3.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'pos',
    data() {
      return {
        tableData: [],
        oftenGoods: [],
        type0Goods: [],
        type1Goods: [],
        type2Goods: [],
        type3Goods: [],
        totalMoney: 0,
        totalCount: 0
      }
    },
    created: function () {
      axios.get('http://www.jspang.com/DemoApi/oftenGoods.php')
        .then(response => {///  相当于ajax的success
//          console.log(response);
          this.oftenGoods = response.data;
        })
        .catch(error => {///  相当于ajax的
//          console.log(error);
          alert('网络错误，不能访问');
        })
      axios.get('http://www.jspang.com/DemoApi/typeGoods.php')
        .then(response => {///  相当于ajax的success
//          console.log(response);
//          this.oftenGoods=response.data;
          this.type0Goods = response.data[0];
          this.type1Goods = response.data[1];
          this.type2Goods = response.data[2];
          this.type3Goods = response.data[3];
        })
        .catch(error => {///  相当于ajax的
//          console.log(error);
          alert('网络错误，不能访问');
        })
    },
    mounted: function () {
      var orderHeight = document.body.clientHeight;
//      console.log(orderHeight);
      document.getElementById('order-list').style.height = orderHeight + 'px';
    },
    methods: {
      addOrderList(goods) {
        this.totalCount = 0; //汇总数量清0
        this.totalMoney = 0;
        console.log(goods);
        console.log(goods.goodsId);
//        商品是否已经存在订单列表
        let isHave = false;
        for (let i = 0; i < this.tableData.length; i++) {
          if (this.tableData[i].goodsId == goods.goodsId) {
            isHave = true;
          }
        }
//        根据判断编写业务逻辑
        if (isHave) {
//          改变列表中商品数量
          let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
          arr[0].count++
        } else {
          let newGoods = {
            goodsId: goods.goodsId,
            goodsName: goods.goodsName,
            price: goods.price,
            count: 1
          }
          this.tableData.push(newGoods);
        }
//        调用计算方法
        this.getAllMoney();
      },
      delSingleGoods(goods) {
        console.log(goods);
        this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
//        调用计算总价格
        this.getAllMoney();
      },
      delAllGood(){
        this.tableData=[];
        this.totalCount = 0; //汇总数量清0
        this.totalMoney = 0;
      },
//      模拟结账
      checkOut(){
        if(this.totalCount!=0){
          this.tableData=[];
          this.totalCount = 0; //汇总数量清0
          this.totalMoney = 0;
          this.$message({
            message:'结账成功。谢谢光临',
            type:'success'
          })
        }else {
          this.$message.error('不能空结');
        }
      },
//      计算全部价格
      getAllMoney() {
        this.totalCount = 0; //汇总数量清0
        this.totalMoney = 0;
        if (this.tableData) {
          //进行数量和价格的汇总计算
          this.tableData.forEach((element) => {
            this.totalCount += element.count;
            this.totalMoney = this.totalMoney + (element.price * element.count);
          });
        }

      }
    }
  }
</script>

<style scoped>
  .goodsName {
    font-size: 12px;
    width: 50%;
  }

  .pos-order {
    background: #f9fafc;
    border-right: 1px solid #c0ccda;
  }

  .div-btn {
    margin-top: 10px;
  }

  .title {
    height: 20px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    padding: 10px;
    text-align: left;
  }

  .often-goods-list ul li {
    list-style: none;
    float: left;
    border: 1px solid #e5e9f2;
    padding: 10px;
    margin: 10px;
    background: #fff;
  }

  .o-price {
    color: #58cfff;
  }

  .good-type {
    clear: both;
  }

  .cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: auto;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;
  }

  .cookList li span {

    display: block;
    float: left;
  }

  .foodImg {
    width: 40%;
  }

  .foodName {
    font-size: 18px;
    padding-left: 10px;
    color: brown;

  }

  .foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
  }

  .totalDiv {
    background: #FFF;
    padding: 10px;
    border-bottom: 1px solid #E5E9F2;
  }
</style>
