<template>
  <div class="pos">
    <el-row>
    	<el-col :span='7' class='pos-order' id='order-list'>
    		<el-tabs type="card">
    			<el-tab-pane label="点餐">
    				<el-table :data='tableData' border style="width:100%">
    					<el-table-column prop="goodsName" label="商品名称"></el-table-column>
    					<el-table-column prop="count" label="数量" width='50px'></el-table-column>
    					<el-table-column prop="price" label="金额" width='70px'></el-table-column>
    					<el-table-column label="操作" width='100px' fixed="right">
    						<template scope="scope">
					            <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
					            <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
					        </template>
    					</el-table-column>
    				</el-table>
    				<div class="total">
    					数量 ： {{ totalCount }} &nbsp;&nbsp;&nbsp;金额 ： {{ totalMoney }}
    				</div>
    				<div class="buttongroup">
    					<el-button type='warning'>挂单</el-button>
    					<el-button type='danger' @click='delAllGoods()'>删除</el-button>
    					<el-button type='success' @click='checkout()'>结账</el-button>
    				</div>
    			</el-tab-pane>
    			<el-tab-pane label="挂单">
    				挂单
    			</el-tab-pane>
    			<el-tab-pane label="外卖">
    				外卖
    			</el-tab-pane>
    		</el-tabs>
    	</el-col> 
    	<el-col :span="17">
    		<div class="often-goods">
    			<div class="title">常用商品</div>
    			<div class="often-goods-list">
    				<ul>
    					<li v-for='goods in oftenGoods' @click="addOrderList(goods)">
    						<span>{{goods.goodsName}}</span>
    						<span class="o-price">￥{{ goods.price}}</span>
    					</li>
    				</ul>
    			</div>
    		</div>

    		<div class="goods-type">
    			<el-tabs type="card">
    				<el-tab-pane label="汉堡">
    					<div>
    						<ul class='cookList'>
							    <li v-for='goods in type0Goods' @click="addOrderList(goods)">
							        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
							        <span class="foodName">{{goods.goodsName}}</span>
							        <span class="foodPrice">￥{{goods.price}}元</span>
							    </li>
							</ul>
    					</div>
    				</el-tab-pane>
    				<el-tab-pane label="小食">
    					<div>
    						<ul class='cookList'>
							    <li v-for='goods in type1Goods' @click="addOrderList(goods)">
							        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
							        <span class="foodName">{{goods.goodsName}}</span>
							        <span class="foodPrice">￥{{goods.price}}元</span>
							    </li>
							</ul>
    					</div>
    				</el-tab-pane>
    				<el-tab-pane label="饮料">
    					<div>
    						<ul class='cookList'>
							    <li v-for='goods in type2Goods' @click="addOrderList(goods)">
							        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
							        <span class="foodName">{{goods.goodsName}}</span>
							        <span class="foodPrice">￥{{goods.price}}元</span>
							    </li>
							</ul>
    					</div>
    				</el-tab-pane>
    				<el-tab-pane label="套餐">
    					<div>
    						<ul class='cookList'>
							    <li v-for='goods in type3Goods' @click="addOrderList(goods)">
							        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
							        <span class="foodName">{{goods.goodsName}}</span>
							        <span class="foodPrice">￥{{goods.price}}元</span>
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
  data(){
  	return{
  		tableData:[],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney:0,
        totalCount:0,
  	}
  },
  created:function(){
  	// 拉取常用商品
  	axios.get('http://jspang.com/DemoApi/oftenGoods.php')
  	.then(response=>{
  		if(response.status==200){
  			this.oftenGoods=response.data;
  		}
  	})
  	.catch(error=>{
  		alert("Net Error!");
  	});
  	// 拉取分类商品数据
  	axios.get('http://jspang.com/DemoApi/typeGoods.php')
  	.then(response=>{
  		if(response.status==200){
  		//this.oftenGoods=response.data;
         this.type0Goods=response.data[0];
         this.type1Goods=response.data[1];
         this.type2Goods=response.data[2];
         this.type3Goods=response.data[3];
  		}
  	})
  	.catch(error=>{
  		alert("Net Error!");
  	});
  },
  mounted:function(){
		var orderHeight=document.body.clientHeight;
      	document.getElementById("order-list").style.height=orderHeight+'px';
  },
  methods:{
  	addOrderList(goods){
  		// 商品是否已经存在于订单列表
  		let isHave=false;
  		for(let i=0;i<this.tableData.length;i++){
  			if(this.tableData[i].goodsId==goods.goodsId){
  				isHave=true;
  			}
  		}
  		// 根据判断的值编写业务逻辑
  		if(isHave){
  			// 改变数量
  			let arr = this.tableData.filter(o => o.goodsId==goods.goodsId);
  			console.log(arr);
 			arr[0].count++;
  		}else{
  			let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
            this.tableData.push(newGoods);
  		}
  		this.getAllMoney();

  	},
  	//删除单个商品
	  delSingleGoods(goods){
	    console.log(goods);
	    this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
	    this.getAllMoney();
	  },
	  //汇总数量和金额
	getAllMoney(){
	    this.totalCount=0;
	    this.totalMoney=0;
	    if(this.tableData){
	            this.tableData.forEach((element) => {
	        this.totalCount+=element.count;
	        this.totalMoney=this.totalMoney+(element.price*element.count);   
	    });
	    }
    },
    // 删除所有商品
    delAllGoods() {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
    },
	checkout() {
	    if (this.totalCount!=0) {
	    	// 这里要有对服务器的操作

	        this.tableData = [];
	        this.totalCount = 0;
	        this.totalMoney = 0;
	        this.$message({ // element提供的组件
	            message: '结账成功，感谢你又为店里出了一份力!',
	            type: 'success'
	        });
	 
	    }else{
	        this.$message.error('不能空结。老板了解你急切的心情！');
	    }
	 
	}
  }
}
</script>

<style scoped>
	.pos-order{
		background-color: #f9fafc;
		border-right: 1px solid #C0CCDA;

	}
	el-tab-pane{
		padding-right:   5px !important;
	}
	.buttongroup{
		margin-top: 20px;
	}

	.title{
		height: 20px;
		border-bottom: 1px solid #d3dce6;
		background-color: #f9fafc;
		padding: 10px;
		text-align: left;
	}
	.often-goods-list ul li{
		cursor: pointer;
		list-style: none;
		float: left;
		border: 1px solid #e5e9f2;
		padding: 10px;
		margin: 10px;
		background-color: #fff;
	}
	.o-price{
		color:#58B7FF;

	}
	.goods-type{
		clear: both;
	}
	.cookList li{
		cursor: pointer;
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
 
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .total{
   	
   }
</style>
