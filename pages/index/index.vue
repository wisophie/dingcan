<template>
	<view class="content">
		<view class="top-bar">
			<view class="top-bar-left" @tap="backone">
				<image src="../../static/images/common/back.png" class="backimg"></image>
			</view>
			<view class="top-bar-center">
				<view class="title">菜单</view>
			</view>
			<view class="top-bar-right" >
				<view class="more-img"  @tap="more">
					<image src="../../static/images/common/more.png"></image>
				</view>
		
			</view>
			
			
			<view class="main">
				
				<view class="bannerline">
					<view class="bannerlinem">
						<view class="meal" @click="num=0" :class="{active:num==0}">中餐</view>
						<view class="meal" @click="num=1" :class="{active:num==1}">晚餐</view>
					</view>
				</view>
				<view class="wrap">
					<view class="tab">
							<view class="date" v-for="(item,index) in dates"  @click="chooseAddNum" :data-index="item" :class="{active:list==item.key}">{{item.date}} {{item.week}}</view>
					</view>
						<scroll-view class="tab2" scroll-y="true" scroll-with-animation="true">
							<view class="item2" v-for="(item,index) in resarrl" :key="index" v-show="num==0" >
							   <view class="able"  v-show="outdatel">{{item.pt}}<br/>{{item.rem}}</view>
								<view class="disable" v-show="outdatel==false">{{item.pt}}<br/>{{item.rem}}<br/>无法预定</view>
							</view>
							<view class="item2" v-for="(item,index) in resarrs" :key="index+'A'" v-show="num==1" >
								<view class="able"  v-show="outdates">{{item.pt}}<br/>{{item.rem}}</view>
								<view class="disable" v-show="outdates==false">{{item.pt}}<br/>{{item.rem}}<br/>无法预定</view>
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
				title: '',
				num:0,
				lunchout:0,
				lunchoutdate:0,
				list:0,
				arr1:[],
				arr2:[],
				arr3:[],
				newarr3l:[],
				newarr3s:[],
				arr4:[],
				newarr4l:[],
				newarr4s:[],
				arr5:[],
				newarr5l:[],
				newarr5s:[],
				resarrl:[],
				resarrs:[],
				dates:[{'date':'05-16','week':'一','key':0},
				{'date':'05-17','week':'二','key':1},
				{'date':'05-18','week':'三','key':2},
				{'date':'05-19','week':'四','key':3},
				{'date':'05-20','week':'五','key':4},
				{'date':'05-21','week':'六','key':5},
				{'date':'05-22','week':'日','key':6},
				{'date':'05-23','week':'一','key':7},
				{'date':'05-24','week':'二','key':8},
				{'date':'05-25','week':'三','key':9},
				{'date':'05-26','week':'四','key':10}],
				dateone:[]
			}
		},
		 computed: {  
			 //按需合并日,周,季菜单
			 //中午的菜单
		      arrLunch: function () {  
				   //日菜单
				  this.newarr3l=this.arr3.filter(item => {
				  return  item.meal=='中餐'&& item.dt=="2022-"+this.dateone.date
			  })
			  //周菜单
			     this.newarr4l=this.arr4.filter(item => {
			  	return item.meal=='中餐'&& item.beg_incl<="2022-"+this.dateone.date&&item.end_excl>="2022-"+this.dateone.date &&item.wk[0]<= this.chinesetonum(this.dateone.week)&&item.wk[1]>= this.chinesetonum(this.dateone.week)
			  })
			  //季菜单
			    this.newarr5l= this.arr5.filter(item => {
			    return item.meal=='中餐'&& item.beg_incl<="2022-"+this.dateone.date
			   })
			   //合并
			   return this.newarr3l.concat(...this.newarr4l).concat(...this.newarr5l)
			},
			
			//按需合并日,周,季菜单
			//晚上的菜单
			  arrSupper: function () {
				  //日菜单
			       this.newarr3s= this.arr3.filter(item => {
			    	return item.meal=='晚餐'&& item.dt=="2022-"+this.dateone.date
			     })
				 //周菜单
				    this.newarr4s=this.arr4.filter(item => {
			    	return item.meal=='晚餐'&& item.beg_incl<="2022-"+this.dateone.date&&item.end_excl>="2022-"+this.dateone.date &&item.wk[0]<= this.chinesetonum(this.dateone.week)&&item.wk[1]>= this.chinesetonum(this.dateone.week)
			     })
				 //季菜单
				   this.newarr5s= this.arr5.filter(item => {
				   return item.meal=='晚餐'&& item.beg_incl<="2022-"+this.dateone.date
				  })
				  //合并
				  return this.newarr3s.concat(...this.newarr4s).concat(...this.newarr5s)
				   
				 
			  } ,
			//计算订餐截止时间
			//中餐截止时间
			outdatel: function () {
				this.lunchout= this.arr1[0].tm_beg+this.arr1[0].tm_end*24*3600
			    this.lunchoutdate=(this.list*24+11)*3600
				return this.lunchout>this.lunchoutdate
			  } ,
			  //晚餐截止时间
			outdates: function () {
				this.supperout= this.arr1[1].tm_beg+this.arr1[1].tm_end*24*3600
			    this.supperoutdate=(this.list*24+16)*3600
				return this.supperout>this.supperoutdate
			  } 
		    } ,
			
		onLoad() {
         this.mealarr1();
		 this.mealarr2();
		 this.mealarr3();
		 this.mealarr4();
		 this.mealarr5();
		 this.clickCompute(0)
		},
		methods: {
			
			//合并菜单函数
			//formArray:主数组，compareArray：需要联合的数组，key：比对的属性，isExpand是否将属性展开到item中
			compareArrayUnion(formArray, compareArray, key,isExpand) {
			  const compareObj = compareArray?.length
			    ? compareArray.reduce((acc, cur) => {
			        return {
			          ...acc,
			          [cur[key]]: cur,
			        };
			      }, {})
			    : {};
			  const resultArray = formArray?.length
			    ? formArray.map((item) => {
			        if(isExpand){
			          item = {
			            ...item,
			            ...compareObj[item[key]]
			          }
			        }else{
			          item.result = compareObj[item[key]] ?? {};
			        }
			        return item;
			      })
			    : [];
			  return resultArray;
			},
			
		   //合并相同项
		   unique(arr){
			
				for(let i=0;i<arr.length;i++)
					for(let j=i+1;j<arr.length;j++){
					if(arr[i].pt === arr[j].pt){
					 arr.splice(j,1);            
					j--;
					 }
					}
				return arr;
		   },
		   
		   //菜单排序
		   compare(a,b){
			   return a.order_by-b.order_by
		   },
		   //汉字转数字
		   chinesetonum(a){
			   let changech = ['一', '二', '三', '四', '五', '六', '日'];
			   switch(a){
			   			case "一":
			   		     return 0;
			   			case "二":
			   			 return 1;
			   			case "三":
			   			 return 2;
			   			case "四":
			   			 return 3;
			   			case "五":
			   			return 4;
			   			case "六":
			   			return 5;
			   			case "日":
			   			return 6;
		   }
			},
			//点击后的菜单生成函数
			clickCompute(num){
				//获取选中日期
				this.dateone=this.dates[num]
				this.list=num
				//生成当天菜单
				//中餐
				this.resarrl=this.unique(this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true))
				//晚餐
				 this.resarrs=this.unique(this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true))
				 //菜单排序
				  this.resarrl.sort(this.compare)
				  this.resarrs.sort(this.compare)
			},
			//获取点击对象日期并显示菜单
			chooseAddNum(e) {
				
			let num= e.currentTarget.dataset.index.key 
				
				if(num==0){
					this.clickCompute(0)
				}else if(num==1){
					this.clickCompute(1)
						
				}else if(num==2){
					this.clickCompute(2)
						
				}else if(num==3){
					this.clickCompute(3)		 
				}else if(num==4){
					this.clickCompute(4)
				}else if(num==5){
					this.clickCompute(5)
				}else if(num==6){
					this.clickCompute(6)
				}else if(num==7){
					this.clickCompute(7)
				}else if(num==8){
					this.clickCompute(8)
				}else if(num==9){
					this.clickCompute(9)
				}else if(num==10){
					this.clickCompute(10)
				}
         },
		
		 //处理时间
		 // changeTime:function(date){
		 // 
		 // },
		 
		 //获取数据
          mealarr1:function(){
			  this.arr1=data.mealarr1();
			  console.log(this.arr1)
		  },
		  mealarr2:function(){
			  this.arr2=data.mealarr2();
			  console.log(this.arr2)
		  },
		  mealarr3:function(){
			  this.arr3=data.mealarr3();
			  console.log(this.arr3)
		  },
		  mealarr4:function(){
			  this.arr4=data.mealarr4();
			  console.log(this.arr4)
		  },
		  mealarr5:function(){
			  this.arr5=data.mealarr5();
			  console.log(this.arr5)
		  }
		}
	}
</script>

<style lang="scss">
	@import "../../commons/css/my.scss";
		
	
	.main {
		padding-top: 90rpx;
		height: 100vh;
		// border:1px solid red;
	}
	.bannerline {
	    height: 45px;
	    margin-top: 5px;
	    border-bottom: 1px solid #edeef0;
	    border-top: 1px solid #edeef0;
	    background-color: #fcfcfc;
	    line-height: 45px;
		// border:1px solid red;
		text-align: center;
	}
	
	
	
	.bannerline .bannerlinem {
		display:flex;
	   justify-content: space-between;
	   // border:1px solid red;
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
			margin-top: 5px;
			background-color: #edeef0;
		}
		.tab2 {
			flex:2;
		    margin-top: 5px;
			padding-bottom: 5vh;
		    border-bottom: 1px solid #edeef0;
		    border-top: 1px solid #edeef0;
		    background-color: #fcfcfc;
		}
	}
	
	.item2 {
	    margin: 20px;
		padding:10rpx;
		border: 1px solid #ccc;
	}
	.active {
	    background-color: #ccc;
	}
	.disable{
		color:  #ccc;
	}
		
	.able{
		background-color: #ccc;
	}
	
</style>
