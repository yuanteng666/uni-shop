<template>
	<view class="content">
		<view class="navbar" :style="{position:headerPosition,top:headerTop}">
			<view class="nav-item" :class="{current: filterIndex === 0}" @click="tabClick(0)">
				综合排序
			</view>
			<view class="nav-item" :class="{current: filterIndex === 1}" @click="tabClick(1)">
				销量优先
			</view>
			<view class="nav-item" :class="{current: filterIndex === 2}" @click="tabClick(2)">
				<text>价格</text>
				<view class="p-box">
					<text :class="{active: priceOrder === 1 && filterIndex === 2}" class="yticon icon-shang"></text>
					<text :class="{active: priceOrder === 2 && filterIndex === 2}" class="yticon icon-shang xia"></text>
				</view>
			</view>
			<text class="cate-item yticon icon-fenlei1" @click="toggleCateMask('show')"></text>
		</view>
		
		<view class="goods-list">
			<view 
				v-for="(item, index) in goodsList" :key="index"
				class="goods-item"
				@click="navToDetailPage(item)"
			>
				<view class="image-wrapper">
					<image :src="item.image" mode="aspectFill"></image>
				</view>
				<text class="title clamp">{{item.title}}</text>
				<view class="price-box">
					<text class="price">{{item.price}}</text>
					<text>已售 {{item.sales}}</text>
				</view>
			</view>
		</view>
		
		<uni-load-more :status="loadingType"></uni-load-more>
		
		<view class="cate-mask" :class="cateMaskState===0 ? 'none' : cateMaskState===1 ? 'show' : ''" @click="toggleCateMask">
			<view class="cate-content" @click.stop.prevent="stopPrevent" @touchmove.stop.prevent="stopPrevent">
				<scroll-view scroll-y class="cate-list">
					<view v-for="item in cateList" :key="item.id">
						<view class="cate-item b-b two">{{item.name}}</view>
						<view 
							v-for="tItem in item.child" :key="tItem.id" 
							class="cate-item b-b" 
							:class="{active: tItem.id==cateId}"
							@click="changeCate(tItem)">
							{{tItem.name}}
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	export default {
		components: {
			uniLoadMore	
		},
		data() {
			return {
				cateMaskState: 0, //分类面板展开状态
				headerPosition:"fixed",
				headerTop:"0px",
				loadingType: 'more', //加载更多状态
				filterIndex: 0, 
				cateId: 0, //已选三级分类id
				priceOrder: 0, //1 价格从低到高 2价格从高到低
				cateList: [],
				goodsList: []
			}
		},
		onLoad(options) {
			// #ifdef H5
			this.headerTop = document.getElementsByTagName('uni-page-head')[0].offsetHeight+'px';
			// #endif
			this.cateId = options.tid;
			this.loadCateList(options.fid,options.sid);
			this.loadData();
		},
		onPageScroll(e) {
				//兼容iOS端下拉时顶部漂移
			if(e.scrollTop>=0){
				this.headerPosition = "fixed";
			}else{
				this.headerPosition = "absolute";
			}
		},
		onPullDownRefresh() {
			this.loadData('refresh');
		},
		onReachBottom() {
			this.loadData();
		},
		methods: {
			//加载分类
			async loadCateList(fid, sid){
				let list = await this.$api.json('cateList');
				let cateList = list.filter(item=>item.pid == fid);
				
				cateList.forEach(item=>{
					let tempList = list.filter(val=>val.pid == item.id);
					item.child = tempList;
				})
				this.cateList = cateList;
			},
			//加载商品 ，带下拉刷新和上滑加载
			async loadData(type='add', loading) {
				//没有更多直接返回
				if(type === 'add'){
					if(this.loadingType === 'nomore'){
						return;
					}
					this.loadingType = 'loading';
				}else{
					this.loadingType = 'more'
				}
				
				let goodsList = await this.$api.json('goodsList');
				if(type === 'refresh'){
					this.goodsList = [];
				}
				//筛选，测试数据直接前端筛选了
				if(this.filterIndex === 1){
					goodsList.sort((a,b)=>b.sales - a.sales)
				}
				if(this.filterIndex === 2){
					goodsList.sort((a,b)=>{
						if(this.priceOrder == 1){
							return a.price - b.price;
						}
						return b.price - a.price;
					})
				}
				
				this.goodsList = this.goodsList.concat(goodsList);
				
				//判断是否还有下一页，有是more  没有是nomore(测试数据判断大于20就没有了)
				this.loadingType  = this.goodsList.length > 20 ? 'nomore' : 'more';
				
				if(type === 'refresh'){
					if(loading == 1){
						uni.hideLoading()
					}else{
						uni.stopPullDownRefresh();
					}
				}
			},
			//筛选点击
			tabClick(index){
				if(this.filterIndex === index && index !== 2){
					return;
				}
				this.filterIndex = index;
				if(index === 2){
					this.priceOrder = this.priceOrder === 1 ? 2: 1;
				}else{
					this.priceOrder = 0;
				}
				uni.pageScrollTo({
					duration: 300,
					scrollTop: 0
				})
				this.loadData('refresh', 1);
				uni.showLoading({
					title: '正在加载'
				})
			},
			//显示分类面板
			toggleCateMask(type){
				let timer = type === 'show' ? 10 : 300;
				let	state = type === 'show' ? 1 : 0;
				this.cateMaskState = 2;
				setTimeout(()=>{
					this.cateMaskState = state;
				}, timer)
			},
			//分类点击
			changeCate(item){
				this.cateId = item.id;
				this.toggleCateMask();
				uni.pageScrollTo({
					duration: 300,
					scrollTop: 0
				})
				this.loadData('refresh', 1);
				uni.showLoading({
					title: '正在加载'
				})
			},
			//详情
			navToDetailPage(item){
				//测试数据没有写id，用title代替
				let id = item.title;
				uni.navigateTo({
					url: '../good-detail/good-detail?id='+id
				})
			},
			stopPrevent(){}
		}
	}
</script>

<style lang="scss">
	page, .content{
		background: $page-color-base;
	}
	.content{
		padding-top: 96upx;
	}
	.navbar{
		position: fixed;
		left: 0;
		top: 0;
		display: flex;
		flex-direction: row;
		background: white;
		width: 100%;
		color: #333;
		font-size: $font-lg;
		height: 80upx;
		align-items: center;
		justify-content: space-around;
		box-shadow: 0 2upx 10upx rgba(0,0,0,.06);
		.nav-item{
			line-height: 80upx;
			display: flex;
			flex-direction: row;
			justify-content: center;
			align-items: center;
		}
		.p-box{
			display: flex;
			flex-direction: column;
			.xia{
				transform: scaleY(-1);
				margin-top: 10upx;
			}
			.active{
				color: $uni-color-primary;
			}
		}
		.active{
			color: $uni-color-primary;
		}
		.yticon{
			width: 30upx;
			height: 14upx;
			line-height: 1;
			margin-left: 4upx;
			font-size: 26upx;
			color: #888;
		}
		.current{
			color: $uni-color-primary;
			border-bottom: 4upx solid $uni-color-primary;
		}
		.cate-item{
			height: 100%;
			width: 80upx;
			font-size: 44upx;
			display: flex;
			justify-content: center;
			align-items: center;
			position: relative;
			&:after{
				content: '';
				position: absolute;
				left: 0;
				top: 50%;
				width: 0upx;
				height: 36upx;
				transform: translateY(-50%);
				border-left: 1upx solid #ddd;
			}
		}
	}
	.goods-list{
		background: white;
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		padding: 0 30upx;
		.goods-item{
			display: flex;
			flex-direction: column;
			width: 48%;
			padding-bottom: 40upx;
			&:nth-child(2n+1){
				margin-right: 4%;
			}
			.image-wrapper{
				width: 100%;
				height: 330upx;
				border-radius: 6upx;
				image{
					width: 100%;
					height: 100%;
					opacity: 1;
				}
			}
			.title{
				color: #333;
				font-size: $font-lg - 2upx;
			}
			.price-box{
				display: flex;
				flex-direction: row;
				justify-content: space-between;
				color: #ddd;
				font-size: $font-lg - 4upx;
				.price{
					color: $uni-color-primary;
					font-size: $font-lg - 2upx;
					position: relative;
					&:before{
						content: "￥";
						font-size: $font-lg - 4upx;
					}
				}
	
			}
		}
	}
.cate-mask{
	position: fixed;
	left: 0;
	bottom: 0;
	top: var(--window-top);
	background: rgba(0,0,0,0);
	transition: 0.3s;
	z-index: 95;
	width: 100%;
	
	&.none{
		display: none;
	}
	&.show{
		background: rgba(0,0,0,.4);
		.cate-content{
			transform: translateX(0);
		}
	}
	.cate-content{
		background: white;
		width: 630upx;
		height: 100%;
		text-align: right;
		float: right;
		transform: translateX(100%);
		transition: 0.3s;
		.cate-list{
			display: flex;
			flex-direction: column;
			height: 100%;
		}
		.cate-item {
			font-size: $font-lg ;
			color: #333;
			height: 90upx;
			line-height: 90upx;
			text-align: left;
			padding-left: 30upx;
		}
		.two{
			font-size: $font-lg - 2upx;
			background: #ddd;
			height: 64upx;
			line-height: 64upx;
		}
		.b-b{
			border: 1upx solid #888;
		}
		.active{
			color: $uni-color-primary;
		}
	}
}
</style>
