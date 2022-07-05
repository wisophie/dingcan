<template>
	<view class="content">
		<view class="top-bar">
			<view class="top-bar-left" >
				<image src="../../static/images/common/back.png" class="backimg"></image>
			</view>
			<view class="top-bar-center">
				<view class="title">菜单</view>
			</view>
			<view class="top-bar-right" >
				<view class="more-img" >
					<image src="../../static/images/common/more.png"></image>
				</view>
		
			</view>
			<view class="main">
				<view class="bannerline">
					<view class="bannerlinem" >
						<view class="meal" v-for="(item,index) in arr1" :key="index" @click="clickMeal(item)" :class="{active1:mealnum==item.meal}"><a class="t">{{item.meal}}</a></view>
					</view>
				</view>
				<view class="wrap">
					<scroll-view class="tab" scroll-y="false" scroll-with-animation="true">
							<view class="date" v-for="(item,index) in dates"  @click="clickDate(item)"  :class="{active:list==item.key}">{{item.date}} {{item.week}}</view>
					</scroll-view>
						<scroll-view class="tab2" scroll-y="true" scroll-with-animation="true">
							<view class="item2" v-for="(item,index) in arrMeal" :key="index"  >
							   <view :class="outdate?'able':'disable'" >{{item.pt}}<br/>{{item.rem}}<a v-show="!outdate"><br/>无法预定</a></view>
							</view>
						</scroll-view>
				</view>
				</view>
			</view>
	</view>
</template>

<script>
	import data from '../../data.js';
	
	export default {
		data() {
			return {
				mealnum:'中餐',
				list:0,
				arr1:[],
				arr2:[],
				arr3:[],
				arr4:[],
				arr5:[],
				dates:[],
				choosedate:[],
			}
		},
		 computed: {  
			 //按需合并日,周,季菜单
		      arrMeal: function () {  
				let set= new Set();
				
				 //日菜单
				 this.arr3.filter(item => {
					return item.meal==this.mealnum
					&&item.dt=="2022-"+this.choosedate.date
				}).forEach(e=>set.add(e.pt));
				
				//周菜单
				  this.arr4.filter(item => {
					return item.meal==this.mealnum
					&&item.beg_incl<="2022-"+this.choosedate.date
					&&item.end_excl>"2022-"+this.choosedate.date 
					&&item.wk[0]<= this.chineseToNum(this.choosedate.week)
					&&item.wk[1]>= this.chineseToNum(this.choosedate.week)
				}).forEach(e=>set.add(e.pt));
				
				//季菜单
				  this.arr5.filter(item => {
				  return item.meal==this.mealnum
				  &&item.beg_incl<="2022-"+this.choosedate.date
				  &&item.end_excl>"2022-"+this.choosedate.date
				 }).forEach(e=>set.add(e.pt));
				 
				 //合并到数组2并排序
				return this.arr2.filter(e=>{
					  return set.has(e.pt)
				 }).sort((a,b)=>a.order_by-b.order_by)
			},
		
			//计算订餐允许时间
			outdate: function (){
				let mealout= this.arr1.filter(item => {
			    	return item.meal==this.mealnum})
			    let now =new Date(),h =now.getHours();
				let data={"早餐":5,"中餐":11.5,"晚餐":17,"下午茶":13} //餐次开始时间
				let outtime=data[this.mealnum]
					return	(mealout[0].tm_beg+mealout[0].tm_end*24*3600)>(this.list*24+outtime)*3600
					        &&h<this.list*24+outtime     //当天时间过了餐次时间无法预定
			  },
			},
			
		onLoad() {
		 this.arrTime();
         this.mealArr();
		 this.clickDate(this.dates[0])
		 this.mealTurn();
		},
		methods: {
			//餐次排序
			mealTurn:function(){
				this.arr1.sort((a,b)=>(a.tm_end*24*3600+a.tm_beg)-(b.tm_end*24*3600+b.tm_beg))
			},
		   //汉字转数字
		   chineseToNum(a){
			   let data={"一":1,"二":2,"三":3,"四":4,"五":5,"六":6,"日":0}
			   return data[a]
			},
			//点击餐次生成菜单
			clickMeal(e){
				this.mealnum=e.meal;
				this.arrMeal			
			},
			//点击日期生成菜单
			clickDate(e){
				this.choosedate=e
				this.list=e.key
				this.arrMeal
			},

		 //日期格式化
		 changeTime:function(e,i){
			 let now =new Date(e),
			 year = now.getFullYear(), // 年
			 month = now.getMonth() + 1, // 月
			 weekday =now.getDay(), //周
			 day = now.getDate(), // 日
			 week = ["日","一","二","三","四","五","六"][weekday];//周几
			 month = month < 10 ? '0' + month : month //月份补 0
			 day = day < 10 ? '0' + day : day //日数补 0
			return {'date':month + "-" + day,'week':week,'key':i};
		 },
		 
		 //生成日期侧边栏数据
		 arrTime:function(){
              let arr = [],len=14;   //len侧边栏长度
			 for (let i = 0; i < len; i++) {
			     let today = new Date();//每次循环将时间初始为当前时间
			     let str = today.getDate()+i;   //当前日期
			 	  today.setDate(str);
				 arr.push(this.changeTime(today,i));
			 }
              this.dates = arr;
		 },
		 
		 //获取数据
		 mealArr:function(){
			  this.arr1=data.mealarr1();
			  this.arr2=data.mealarr2();
			  this.arr3=data.mealarr3();
			  this.arr4=data.mealarr4();
			  this.arr5=data.mealarr5();
		   },
		 }
	}
</script>

<style lang="scss">
	@import "../../commons/css/my.scss";
		
	
	.main {
		padding-top: 90rpx;
		height: 100vh;
	}
	.bannerline {
	    height: 45px;
	    background-color: #dfdfdf;
	    line-height: 45px;
		text-align: center;
		color:#555555;
	}
	

	.bannerline .bannerlinem {
		display:flex;
	   justify-content: space-between;
	 
	   
	   .meal{
		flex:1;
		
	   }
	}
	
	.wrap{
		display:flex;
		height: 83vh;
		justify-content: space-between;
		text-align: center;
		.tab{
			flex:1;
			margin-top: 4px;
			padding-bottom: 4vh;
			background-color: #edeef0;
			::-webkit-scrollbar{
					    width: 0;
					    height: 0;
					    color: transparent;
					}
		}
		.tab2 {
			flex:2;
		    margin-top: 4px;
			padding-bottom: 5vh;
		    border-bottom: 1px solid #edeef0;
		    background-color: #fcfcfc;
		}
	}
	
	.item2 {
	    margin: 20px;
		padding:10rpx;
		border: 1px solid #ccc;
	}
	.active {
	    background-color: #ffbd14;
		
	}
	.active1 {
	    background-color: #ffffff;
		.t{
			padding-bottom:10rpx;
			border-bottom:2px solid #ffbd14;
			color:#ffaa00;
		}
	}
	.disable{
		color:  #ccc;
	}
		
	.able{
		background-color: #ccc;
	}
	
</style>
