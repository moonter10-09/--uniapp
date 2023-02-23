<template>
	<view>
		<view class="scroll-view-container">
			<!-- 左侧滑动区域 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height:viewHeight+'px'}">
				<block v-for="(item,index) in cateList" :key="index">
					<view :class="['left-scroll-view-item', index === activeIndex ? 'active' : '']" @click="changeActiveIndex(index)">{{item.cat_name}}</view>
				</block>
			</scroll-view>
			<!-- 右侧滑动区域 -->
			<scroll-view class="right-scroll-view" scroll-y="true" :style="{height:viewHeight+'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item2,index2) in cateList2" :key="index2">
					<view class="cate-lv2-title">/{{item2.cat_name}}/</view>
					<view class="cate-1v3-list" >
						<view class="cate-1v3-item" v-for="(item3,index3) in item2.children" :key="index3"
						@click="toGoodsList(item3)">
							<img :src='item3.cat_icon'>
							<text>{{item3.cat_name}}</text>
						</view>
					</view> 
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 当前设备可用的高度
				viewHeight:0,
				// 分类数据列表
				cateList: [],
				cateList2:[],
				// 当前激活的索引值
				activeIndex:0,
				scrollTop:0
			};
		},
		onLoad(){
			const sysInfo = uni.getSystemInfoSync()
			this.viewHeight = sysInfo.windowHeight
			this.getCateList()
		},
		methods:{
			getCateList(){
				uni.$http.get('/api/public/v1/categories').then(res=>{
					if(res.statusCode == 200){
						console.log(res,'res');
						this.cateList = res.data.message
						console.log(this.cateList,'this.cateList');
						this.cateList2 = res.data.message[0].children
					}
					else{
						uni.showToast({
							title:res.errMsg,
							icon:'none'
						})
					}
				})
			},
			changeActiveIndex(index){
				this.activeIndex = index
				this.cateList2 = this.cateList[index].children
				this.scrollTop = this.scrollTop ==  0 ? 1 : 0
			},
			toGoodsList(val){
				uni.navigateTo({
					url:`/subpkg/goods_list/goods_list?cid=${val.cat_id}`
				})
			}
		}
	}
</script>

<style lang="scss">
	.scroll-view-container{
		display: flex;
	}
	.left-scroll-view{
		width: 240rpx;
	}
	.left-scroll-view-item{
		line-height: 120rpx;
		font-size: 24rpx;
		text-align: center;
		background-color: #f7f7f7;
		
		&.active{
			position: relative;
			background-color: #FFFFFF;
			&::before{
				display: block;
				content: '';
				width: 6rpx;
				background-color: red;
				height: 60rpx;
				position: absolute;
				top: 50%;
				left: 0;
				transform: translateY(-50%);
			}
		}
	}
	.cate-lv2-title{
		font-size: 24rpx;
		font-weight: bold;
		padding: 30rpx 0;
		text-align: center;
	}
	.cate-1v3-list{
		flex-wrap: wrap;
		display: flex;
		.cate-1v3-item{
			width: 33.33%;
			margin-bottom: 20rpx;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			image {
				width: 120rpx;
				height: 120rpx;
			}
			text {
				font-size: 12px;
			}
		}
		
	}	
</style>
