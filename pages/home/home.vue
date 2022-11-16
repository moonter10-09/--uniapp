<template>
	<view>
		<!-- 轮播图的区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
			<swiper-item v-for="(item,index) in swiperList" :key="index">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id">
					<img :src="item.image_src" :alt="item.goods_id">
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航区域 -->
		<view class="nav_list">
			<view class="nav_item" v-for="(item,index) in navList" :key="index" @click="navClick(item)">
				<img :src="item.image_src" alt="">
			</view>
		</view>
		<!-- 楼层区域 -->
		<view class="floor_list">
			<!-- 楼层的每一个item -->
			<view class="floor_item" v-for="(item,index) in floorList" :key="index">
				<!-- 楼层每一个item的标题 -->
				<img class="floor_title" :src="item.floor_title.image_src" alt="">
				<view class="floor_img_box">
					<!-- 左侧图片的盒子 -->
					<navigator class="left_img_box" :url="item.product_list[0].url" open-type="navigate">
						<img :src="item.product_list[0].image_src" alt=""
							:style="{width:item.product_list[0].image_width+'rpx'}" mode="widthFix">
					</navigator>
					<!-- 右侧4个盒子图片 -->
					<view class="right_img_box">
						<navigator  v-for="(item1,index1) in item.product_list" :key="index1" v-show="index1 !== 0" :url="item1.url">
							<img :src="item1.image_src" alt=""  mode="widthFix"
								:style="{width:item1.image_width+'rpx'}">
						</navigator >
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 轮播图的数据列表
				swiperList: [],
				// 分类导航的数据列表
				navList: [],
				// 楼层的数据列表
				floorList: [],
			};
		},
		onLoad() {
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods: {
			async getSwiperList() {
				const {
					data: res,
					statusCode
				} = await uni.$http.get('/api/public/v1/home/swiperdata')
				if (statusCode != '200') {
					uni.$showMsg()
					return
				} else {
					this.swiperList = res.message
				}
			},
			async getNavList() {
				const {
					data: res,
					statusCode
				} = await uni.$http.get('/api/public/v1/home/catitems')
				if (statusCode != 200) {
					uni.$showMsg()
					return
				} else {
					this.navList = res.message
				}
			},
			async getFloorList() {
				const {
					data: res,
					statusCode
				} = await uni.$http.get('/api/public/v1/home/floordata')
				if (statusCode != 200) {
					uni.$showMsg()
					return
				} else {
					this.floorList = res.message
					this.floorList.forEach(floor => {
					    floor.product_list.forEach(prod => {
					      prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					    })
					  })
				}
			},
			navClick(item) {
				const {
					name
				} = item
				console.log(name);
				if (name == "分类") {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}

			}
		}
	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;

		.swiper-item,
		img {
			width: 100%;
			height: 100%;
		}
	}

	.nav_list {
		display: flex;
		justify-content: space-between;
		margin: 30rpx 0;

		.nav_item,
		img {
			width: 128rpx;
			height: 140rpx;
		}
	}

	.floor_title {
		width: 100%;
		height: 60rpx;
	}

	.floor_img_box {
		display: flex;
		padding-left: 12rpx;
	}

	.right_img_box {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}
</style>
