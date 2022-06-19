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
	import myfunction from '../../commons/js/myfunction.js';
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
				arr4:[],
				arr5:[],
				res:[],
				resarrl:[],
				resarrs:[],
				dates:[{'date':'05-16','week':'一','key':0},{'date':'05-17','week':'二','key':1},{'date':'05-18','week':'三','key':2},{'date':'05-19','week':'四','key':3},{'date':'05-20','week':'五','key':4},{'date':'05-21','week':'六','key':5},{'date':'05-22','week':'日','key':6},{'date':'05-23','week':'一','key':7},{'date':'05-24','week':'二','key':8}],
				dateone:[]
			}
		},
		 computed: {  
		      arrLunch: function () {  
						  return this.res=this.arr3.filter(item => {
						  return  item.meal=='中餐'&& item.dt=="2022-"+this.dateone.date
					  })
			},
			  arrSupper: function () {
			    return  this.arr3.filter(item => {
			    	return item.meal=='晚餐'&& item.dt=="2022-"+this.dateone.date
			     }) 
			  } ,
			outdatel: function () {
				this.lunchout= this.arr1[0].tm_beg+this.arr1[0].tm_end*24*3600
			    this.lunchoutdate=(this.list*24+11)*3600
				return this.lunchout>this.lunchoutdate
			  } ,
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
		 this.dateone=this.dates[0];
		 this.list=0;
		 this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
		 this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
		},
		methods: {
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
		
			
			//获取点击对象
			chooseAddNum(e) {
				
				// for(let i=0 ;i<e.currentTarget.dataset.index.length;i++){
				// 	if(e.currentTarget.dataset.index.key==i){
				// 				this.dateone=this.dates[i]
				// 				this.list=i
				// 				console.log(this.dates[i])
							
				// 	}
				// }
				
				if(e.currentTarget.dataset.index.key==0){
							this.dateone=this.dates[0]
							this.list=0
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							 this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
							// console.log(this.outdate)
				}else if(e.currentTarget.dataset.index.key==1){
							this.dateone=this.dates[1]
							this.list=1
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
						     this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==2){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[2]
							this.list=2
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==3){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[3]
							this.list=3
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==4){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[4]
							this.list=4
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==5){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[5]
							this.list=5
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==6){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[6]
							this.list=6
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				
				}else if(e.currentTarget.dataset.index.key==7){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[7]
							this.list=7
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}else if(e.currentTarget.dataset.index.key==8){
							// this.dates[2].date='2022-05-18'
							this.dateone=this.dates[8]
							this.list=8
							this.resarrl=this.compareArrayUnion(this.arrLunch,this.arr2,"pt",true)
							this.resarrs=this.compareArrayUnion(this.arrSupper,this.arr2,"pt",true)
				}
         },
		
		 //处理时间
		 // changeTime:function(date){
		 // 	return myfunction.dateTime1(date)
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
