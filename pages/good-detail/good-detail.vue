<template>
	<view class="container">
		<view class="carousel">
			<swiper indicator-dots circular=true duration="400">
				<swiper-item class="swiper-item" v-for="(item,index) in imgList" :key="index">
					<view class="image-wrapper">
						<image
							:src="item.src" 
							class="loaded" 
							mode="aspectFill"
						></image>
					</view>
				</swiper-item>
			</swiper>
			
		</view>
		<view class="introduce-section">
			<text class="title">恒源祥2019春季长袖白色t恤 新款春装</text>
			<view class="price-box">
				<text class="price-tip">¥</text>
				<text class="price">341.6</text>
				<text class="m-price">¥488</text>
				<text class="coupon-tip">7折</text>
			</view>
			<view class="bot-row">
				<text>销量: 108</text>
				<text>库存: 4690</text>
				<text>浏览量: 768</text>
			</view>
		</view>
		<!--  分享 -->
		<view class="share-section" @click="share">
			<view class="share-icon">
				<text class="yticon icon-xingxing"></text>
				 返
			</view>
			<text class="tit">该商品分享可领49减10红包</text>
			<text class="yticon icon-bangzhu1"></text>
			<view class="share-btn">
				立即分享
				<text class="yticon icon-you"></text>
			</view>
		</view>
		
		<view class="c-list">
			<view class="c-row b-b" @click="togglePopup('bottom-share')">
				<text class="tit">购买类型</text>
				<view class="con">
					<text class="selected-text" v-for="(sItem, sIndex) in specSelected" :key="sIndex">
						{{sItem.name}}
					</text>
				</view>
				<text class="yticon icon-you"></text>
			</view>
			<view class="c-row b-b">
				<text class="tit">优惠券</text>
				<text class="con t-r red">领取优惠券</text>
				<text class="yticon icon-you"></text>
			</view>
			<view class="c-row b-b">
				<text class="tit">促销活动</text>
				<view class="con-list">
					<text>新人首单送20元无门槛代金券</text>
					<text>订单满50减10</text>
					<text>订单满100减30</text>
					<text>单笔购买满两件免邮费</text>
				</view>
			</view>
			<view class="c-row b-b">
				<text class="tit">服务</text>
				<view class="bz-list con">
					<text>7天无理由退换货 ·</text>
					<text>假一赔十 ·</text>
				</view>
			</view>
		</view>
		
		<!-- 评价 -->
		<view class="eva-section">
			<view class="e-header">
				<text class="tit">评价</text>
				<text>(86)</text>
				<text class="tip">好评率 100%</text>
				<text class="yticon icon-you"></text>
			</view> 
			<view class="eva-box">
				<image class="portrait" src="http://img3.imgtn.bdimg.com/it/u=1150341365,1327279810&fm=26&gp=0.jpg" mode="aspectFit"></image>
				<view class="right">
					<text class="name">Leo yo</text>
					<text class="con">商品收到了，79元两件，质量不错，试了一下有点瘦，但是加个外罩很漂亮，我很喜欢</text>
					<view class="bot">
						<text class="attr">购买类型：XL 红色</text>
						<text class="time">2019-04-01 19:21</text>
					</view>
				</view>
			</view>
		</view>
		
		<view class="detail-desc">
			<view class="d-header">
				<text>图文详情</text>
			</view>
			<rich-text :nodes="desc"></rich-text>
		</view>
		
		<!-- 底部操作菜单 -->
		<view class="page-bottom">
			<navigator url="/pages/index/index" open-type="switchTab" class="p-b-btn">
				<text class="yticon icon-xiatubiao--copy"></text>
				<text>首页</text>
			</navigator>
			<navigator url="/pages/cart/cart" open-type="switchTab" class="p-b-btn">
				<text class="yticon icon-gouwuche"></text>
				<text>购物车</text>
			</navigator>
			<view class="p-b-btn" :class="{active: favorite}" @click="toFavorite">
				<text class="yticon icon-shoucang"></text>
				<text>收藏</text>
			</view>
			
			<view class="action-btn-group">
				<button  class=" action-btn no-border buy-now-btn" @click="buy">立即购买</button>
				<button  class=" action-btn no-border add-cart-btn">加入购物车</button>
			</view>
		</view>
		
		<!-- 规格-模态层弹窗 -->
		
		<uni-popup :show="type === 'bottom-share'" position="bottom" @hidePopup="togglePopup('')">
			<view 
				class="popup spec" 
				:class="specClass"
				@touchmove.stop.prevent="stopPrevent"
				@click="toggleSpec"
			>
				<!-- 遮罩层 -->
				<view class="mask"></view>
				<view class="layer attr-content" @click.stop="stopPrevent">
					<view class="a-t">
						<image src="https://gd3.alicdn.com/imgextra/i3/0/O1CN01IiyFQI1UGShoFKt1O_!!0-item_pic.jpg_400x400.jpg"></image>
						<view class="right">
							<text class="price">¥328.00</text>
							<text class="stock">库存：188件</text>
							<view class="selected">
								已选：
								<text class="selected-text" v-for="(sItem, sIndex) in specSelected" :key="sIndex">
									{{sItem.name}}
								</text>
							</view>
						</view>
					</view>
					<view v-for="(item,index) in specList" :key="index" class="attr-list">
						<text>{{item.name}}</text>
						<view class="item-list">
							<text 
								v-for="(childItem, childIndex) in specChildList" 
								v-if="childItem.pid === item.id"
								:key="childIndex" class="tit"
								:class="{selected: childItem.selected}"
								@click="selectSpec(childIndex, childItem.pid)"
							>
								{{childItem.name}}
							</text>
						</view>
					</view>
					<button class="btn" @click="toggleSpec">完成</button>
				</view>
			</view>
			
		</uni-popup>
		
	</view>
</template>

<script>
	import uniPopup from '../../components/uni-popup/uni-popup.vue'
	import share from '../../components/share.vue'
	export default {
		components:{
			uniPopup,
			share
		},
		data() {
			return {
				type:'',
				specSelected:[],
				imgList: [
					{
						src: 'https://gd3.alicdn.com/imgextra/i3/0/O1CN01IiyFQI1UGShoFKt1O_!!0-item_pic.jpg_400x400.jpg'
					},
					{
						src: 'https://gd3.alicdn.com/imgextra/i3/TB1RPFPPFXXXXcNXpXXXXXXXXXX_!!0-item_pic.jpg_400x400.jpg'
					},
					{
						src: 'https://gd2.alicdn.com/imgextra/i2/38832490/O1CN01IYq7gu1UGShvbEFnd_!!38832490.jpg_400x400.jpg'
					}
				],
				specList: [
					{
						id: 1,
						name: '尺寸',
					},
					{	
						id: 2,
						name: '颜色',
					},
				],
				specChildList: [
					{
						id: 1,
						pid: 1,
						name: 'XS',
					},
					{
						id: 2,
						pid: 1,
						name: 'S',
					},
					{
						id: 3,
						pid: 1,
						name: 'M',
					},
					{
						id: 4,
						pid: 1,
						name: 'L',
					},
					{
						id: 5,
						pid: 1,
						name: 'XL',
					},
					{
						id: 6,
						pid: 1,
						name: 'XXL',
					},
					{
						id: 7,
						pid: 2,
						name: '白色',
					},
					{
						id: 8,
						pid: 2,
						name: '珊瑚粉',
					},
					{
						id: 9,
						pid: 2,
						name: '草木绿',
					},
				],
				desc: `
					<div style="width:100%">
						<img style="width:100%;display:block;" src="https://gd3.alicdn.com/imgextra/i4/479184430/O1CN01nCpuLc1iaz4bcSN17_!!479184430.jpg_400x400.jpg" />
						<img style="width:100%;display:block;" src="https://gd2.alicdn.com/imgextra/i2/479184430/O1CN01gwbN931iaz4TzqzmG_!!479184430.jpg_400x400.jpg" />
						<img style="width:100%;display:block;" src="https://gd3.alicdn.com/imgextra/i3/479184430/O1CN018wVjQh1iaz4aupv1A_!!479184430.jpg_400x400.jpg" />
						<img style="width:100%;display:block;" src="https://gd4.alicdn.com/imgextra/i4/479184430/O1CN01tWg4Us1iaz4auqelt_!!479184430.jpg_400x400.jpg" />
						<img style="width:100%;display:block;" src="https://gd1.alicdn.com/imgextra/i1/479184430/O1CN01Tnm1rU1iaz4aVKcwP_!!479184430.jpg_400x400.jpg" />
					</div>
				`,
				favorite:true
			}
		},
		async	onLoad(options) {
			//接收传值,id里面放的是标题，因为测试数据并没写id 
			let id = options.id;
			if(id){
				this.$api.msg(`点击了${id}`);
			}
			
			
			//规格 默认选中第一条
			this.specList.forEach(item=>{
				for(let cItem of this.specChildList){
					if(cItem.pid === item.id){
						this.$set(cItem, 'selected', true);
						this.specSelected.push(cItem);
						break; //forEach不能使用break
					}
				}
			})
			this.shareList = await this.$api.json('shareList');
		},
		methods: {
			togglePopup(type) {
				this.type = type
			},
			//规格弹窗开关
			toggleSpec() {
				if(this.specClass === 'show'){
					this.specClass = 'hide';
					setTimeout(() => {
						this.specClass = 'none';
					}, 250);
				}else if(this.specClass === 'none'){
					this.specClass = 'show';
				}
			},
			//选择规格
			selectSpec(index, pid){
				let list = this.specChildList;
				list.forEach(item=>{
					if(item.pid === pid){
						this.$set(item, 'selected', false);
					}
				})
			
				this.$set(list[index], 'selected', true);
				//存储已选择
				/**
				 * 修复选择规格存储错误
				 * 将这几行代码替换即可
				 * 选择的规格存放在specSelected中
				 */
				this.specSelected = []; 
				list.forEach(item=>{ 
					if(item.selected === true){ 
						this.specSelected.push(item); 
					} 
				})
				
			},
			//分享
			share(){
				this.$refs.share.toggleMask();	
			},
			//收藏
			toFavorite(){
				this.favorite = !this.favorite;
			},
			buy(){
				uni.navigateTo({
					url: `/pages/order/createOrder`
				})
			},
			stopPrevent(){}
		}
	}
</script>

<style lang="scss">
page{
	background: $page-color-base;
	padding-bottom: 160upx;
}
.carousel {
		height: 750upx;
		position:relative;
		swiper{
			height: 100%;
		}
		.image-wrapper{
			width: 100%;
			height: 100%;
		}
		.swiper-item {
			display: flex;
			justify-content: center;
			align-content: center;
			height: 750upx;
			overflow: hidden;
			image {
				width: 100%;
				height: 100%;
			}
		}
		
	}
.introduce-section{
	background: #fff;
	padding: 20upx 30upx;
	.title{
		font-size: 32upx;
		color: $font-color-dark;
		height: 50upx;
		line-height: 50upx;
	}
	.price-box{
		display:flex;
		align-items:baseline;
		height: 64upx;
		padding: 10upx 0;
		font-size: 26upx;
		color:$uni-color-primary;
	}
	.coupon-tip{
		align-items: center;
		padding: 4upx 10upx;
		background: $uni-color-primary;
		font-size: $font-sm;
		color: #fff;
		border-radius: 6upx;
		line-height: 1;
		transform: translateY(-4upx); 
	}
	.price{
		font-size: $font-lg + 2upx;
	}
	.m-price{
		margin:0 12upx;
		color: $font-color-light;
		text-decoration: line-through;
	}
	.bot-row{
		display:flex;
		align-items:center;
		height: 50upx;
		font-size: $font-sm;
		color: $font-color-light;
		text{
			flex: 1;
		}
	}
}

/* 分享 */
	.share-section{
		display:flex;
		align-items:center;
		color: $font-color-base;
		background: linear-gradient(left, #fdf5f6, #fbebf6);
		padding: 12upx 30upx;
		.share-icon{
			display:flex;
			align-items:center;
			width: 70upx;
			height: 30upx;
			line-height: 30upx;
			border: 1px solid $uni-color-primary;
			border-radius: 4upx;
			position:relative;
			overflow: hidden;
			font-size: 22upx;
			color: $uni-color-primary;
			&:after{
				content: '';
				width: 50upx;
				height: 50upx;
				border-radius: 50%;
				left: -20upx;
				top: -12upx;
				position:absolute;
				background: $uni-color-primary;
			}
		}
		.icon-xingxing{
			z-index: 1;
			font-size: 24upx;
			margin-left: 2upx;
			margin-right: 10upx;
			color: #fff;
			line-height: 1;
		}
		.tit{
			font-size: $font-base;
			margin-left:10upx;
		}
		.icon-bangzhu1{
			padding: 10upx;
			font-size: 30upx;
			line-height: 1;
		}
		.share-btn{
			flex: 1;
			text-align:right;
			font-size: $font-sm;
			color: $uni-color-primary;
		}
		.icon-you{
			font-size: $font-sm;
			margin-left: 4upx;
			color: $uni-color-primary;
		}
	}
	
	.c-list{
		font-size: $font-sm + 2upx;
		color: $font-color-base;
		background: #fff;
		.c-row{
			display: flex;
			flex-direction: row;
			align-items: center;
			padding: 20upx 30upx;
		}
		.b-b{
			border-bottom: 1upx solid #999999;
		}
		.tit{
			width: 180upx;
		}
		.con{
			color: $font-color-dark;
			.selected-text{
				margin-right: 10upx;
			}
		}
		.icon-you{
			flex: 1;
			text-align: right;
		}
		.red{
			color: $uni-color-primary;
		}
		.con-list{
			display: flex;
			flex-direction: column;
			color: $font-color-dark;
			text{
				padding-top: 5upx;
				padding-bottom: 5upx;
			}
		}
		.bz-list{
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			text{
				margin-right: 30upx;
			}
		}
	}
	.eva-section{
		background: white;
		padding: 20upx 30upx;
		display: flex;
		flex-direction: column;
		margin-top: 26upx;
		.e-header{
			display: flex;
			flex-direction: row;
			font-size: $font-sm + 2upx;
			color: $font-color-light;
			padding-top: 10upx;
			padding-bottom: 10upx;
			.tit{
				font-size: $font-base + 2upx;
				color: $font-color-dark;
			}
			.tip{
				flex: 1;
				text-align: right;
			}
			.icon-you{
				margin-left: 10upx;
			}
		}
		.eva-box{
			display: flex;
			flex-direction: row;
			padding: 20upx 0;
			.portrait{
				flex-shrink: 0;
				width: 80upx;
				height: 80upx;
				border-radius: 100px;
			}
			.right{
				display: flex;
				flex-direction: column;
				margin-left: 26upx;
				font-size: $font-base;
				color: $font-color-base;
				.name{
					font-size: $font-lg+2upx;
				}
				.con{
					font-size: $font-base;
					color: $font-color-dark;
					margin-top: 20upx;
					margin-bottom: 20upx;
				}
				.bot{
					font-size: $font-sm;
					color:$font-color-light;
					display: flex;
					flex-direction: row;
					justify-content: space-between;
				}
			}
		}
		
	}
.detail-desc{

	
	background: #fff;
	margin-top: 16upx;
	.d-header{
		display: flex;
		justify-content: center;
		align-items: center;
		height: 80upx;
		font-size: $font-base + 2upx;
		color: $font-color-dark;
		position: relative;
			
		text{
			padding: 0 20upx;
			background: #fff;
			position: relative;
			z-index: 1;
		}
		&:after{
			position: absolute;
			left: 50%;
			top: 50%;
			transform: translateX(-50%);
			width: 300upx;
			height: 0;
			content: '';
			border-bottom: 1px solid #ccc; 
		}
	}
}
.page-bottom{
	display: flex;
	flex-direction: row;
	justify-content: space-around;
	background: rgba(255,255,255,.9);
	box-shadow: 0 0 20upx 0 rgba(0,0,0,.5);
	border-radius: 16upx;
	margin: 20upx;
	padding: 10upx;
	position: fixed;
	bottom: 4upx;
	left: 0;
	width: 95%;
	
	.p-b-btn{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.yticon{
		font-size:40upx;
		color: gray;
		margin-left: 20upx;
		margin-right: 20upx;
	}
	.active, .active .yticon{
		color: $uni-color-primary;
	}
	text{
		margin-top: 10upx;
		font-size: $font-sm;
	}
	.action-btn-group{
		display: flex;
		flex-direction: row;
		
		box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
		background: linear-gradient(to right, #ffac30,#fa436a,#F56C6C);
		margin-left: 20upx;
		border-radius: 100upx;
		color: white;
		padding: 10upx;
		position:relative;
		&:before{
			content: '';
			position:absolute;
			top: 50%;
			right: 50%;
			transform: translateY(-50%);
			height: 28upx;
			width: 0;
			border-right: 1px solid rgba(255,255,255,.5);
		}
		.action-btn{
			display:flex;
			align-items: center;
			justify-content: center;
			width: 180upx;
			height: 100%;
			font-size: $font-lg ;
			padding: 0;
			border-radius: 0;
			background: transparent;
			color: white;
		}
	}
}
.popup{
	.mask{
		background: #999999;
		opacity: 0.3;
	}
	.layer{
		background: white;
		padding: 20upx;
		.a-t{
			display: flex;
			flex-direction: row;
			text{
				padding-top: 10upx;
				padding-bottom: 10upx;
			}
			image{
				width: 170upx;
				height: 170upx;
				border-radius: 10upx;
				margin-bottom: -50upx;
			}
			.right{
				margin-left: 20upx;
				display: flex;
				flex-direction: column;
				height: 170upx;
				font-size: $font-base+2upx;
				color: gray;
				vertical-align: bottom;
				.price{
					font-size: $font-base + 4upx;
					color: $uni-color-primary;
				}
			}
		}
		.attr-list{
			margin-top: 20upx;
			.item-list{
				margin-bottom: 30upx;
				margin-top: 20upx;
				text{
					background: lightgrey;
					border-radius: 100upx;
					color: black;
					padding: 15upx;
					font-size: $font-lg;
					margin-right: 20upx;
					opacity: 0.6;
					min-width: 70upx;
					text-align: center;
					display: inline-block;
				}
				.selected{
					background: #F56C6C;
					color: $uni-color-primary;
				}
			}
			
		}
	}
	.btn{
		background: $uni-color-primary;
		border-radius: 100upx;
		color: white;
	}
}
</style>
