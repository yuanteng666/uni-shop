<template>
	<view class="container">
		<view class="empty" v-if="!hasLogin || empty">
			<image src="../../static/emptyCart.jpg" mode="aspectFill"></image>
			<view class="empty-tips" v-if="hasLogin">
				空空如也
				<navigator class="navigator" v-if="hasLogin" url="../index/index" open-type="switchTab">随便逛逛></navigator>
			</view>
			<view class="empty-tips" v-else>
				空空如也				
				<view class="navigator" @click="navToLogin">去登陆></view>
			</view>
		</view>
		<view v-else>
			<!-- 列表 -->
			<view class="cart-list">
				<block v-for="(item, index) in cartList" :key="item.id">
					<view
						class="cart-item" 
						:class="{'b-b': index!==cartList.length-1}"
					>
						<view class="image-wrapper">
							<image :src="item.image" 
								:class="[item.loaded]"
								mode="aspectFill" 
								lazy-load 
								@load="onImageLoad('cartList', index)" 
								@error="onImageError('cartList', index)"
							></image>
							<view 
								class="yticon icon-xuanzhong2 checkbox"
								:class="{checked: item.checked}"
								@click="check('item', index)"
							></view>
						</view>
						<view class="item-right">
							<text class="clamp title">{{item.title}}</text>
							<text class="attr">{{item.attr_val}}</text>
							<text class="price">¥{{item.price}}</text>
							<uni-number-box 
								class="step"
								:min="1" 
								:max="item.stock"
								:value="item.number>item.stock?item.stock:item.number"
								:isMax="item.number>=item.stock?true:false"
								:isMin="item.number===1"
								:index="index"
								@eventChange="numberChange"
							></uni-number-box>
						</view>
						<text class="del-btn yticon icon-fork" @click="deleteCartItem(index)"></text>
					</view>
				</block>
			</view>	
			
				<!-- 底部菜单栏 -->
			<view class="action-section">
				<view class="checkbox">
					<image 
						:src="allChecked?'/static/selected.png':'/static/select.png'" 
						mode="aspectFit"
						@click="check('all')"
					></image>
					<view class="clear-btn" :class="{show: allChecked}" @click="clearCart">
						清空
					</view>
				</view>
				<view class="total-box">
					<text class="price">¥{{total}}</text>
					<text class="coupon">
						已优惠
						<text>74.35</text>
						元
					</text>
				</view>
				<button type="primary" class="no-border confirm-btn" @click="createOrder">去结算</button>
			</view>
		</view>
		
	
	</view>
</template>

<script>
	import {mapState} from 'vuex';
	import uniNumberBox from '@/components/uni-number-box.vue'
	export default {
		components: {
			uniNumberBox
		},
		data() {
			return {
				total: 0, //总价格
				allChecked: false, //全选状态  true|false
				empty: false, //空白页现实  true|false
				cartList: [],
			}
		},
		onLoad(){
			this.loadData();
		},
		watch:{
			//显示空白页
			cartList(e){
				let empty = e.length === 0 ? true: false;
				if(this.empty !== empty){
					this.empty = empty;
				}
			}
		},
		computed: {
			...mapState(['hasLogin'])
		},
		methods: {
						//请求数据
			async loadData(){
				let list = await this.$api.json('cartList'); 
				let cartList = list.map(item=>{
					item.checked = true;
					return item;
				});
				this.cartList = cartList;
				this.calcTotal();  //计算总价
			},
			//监听image加载完成
			onImageLoad(key, index) {
				this.$set(this[key][index], 'loaded', 'loaded');
			},
			//监听image加载失败
			onImageError(key, index) {
				this[key][index].image = '/static/errorImage.jpg';
			},
			navToLogin(){
				uni.navigateTo({
					url: '/pages/login/login'
				})
			},
			 //选中状态处理
			check(type, index){
				if(type === 'item'){
					this.cartList[index].checked = !this.cartList[index].checked;
				}else{
					const checked = !this.allChecked
					const list = this.cartList;
					list.forEach(item=>{
						item.checked = checked;
					})
					this.allChecked = checked;
				}
				this.calcTotal(type);
			},
			//数量
			numberChange(data){
				this.cartList[data.index].number = data.number;
				this.calcTotal();
			},
			//删除
			deleteCartItem(index){
				let list = this.cartList;
				let row = list[index];
				let id = row.id;
			
				this.cartList.splice(index, 1);
				this.calcTotal();
				uni.hideLoading();
			},
			//清空
			clearCart(){
				uni.showModal({
					content: '清空购物车？',
					success: (e)=>{
						if(e.confirm){
							this.cartList = [];
						}
					}
				})
			},
			//计算总价
			calcTotal(){
				let list = this.cartList;
				if(list.length === 0){
					this.empty = true;
					return;
				}
				let total = 0;
				let checked = true;
				list.forEach(item=>{
					if(item.checked === true){
						total += item.price * item.number;
					}else if(checked === true){
						checked = false;
					}
				})
				this.allChecked = checked;
				this.total = Number(total.toFixed(2));
			},
			//创建订单
			createOrder(){
				let list = this.cartList;
				let goodsData = [];
				list.forEach(item=>{
					if(item.checked){
						goodsData.push({
							attr_val: item.attr_val,
							number: item.number
						})
					}
				})
			
				uni.navigateTo({
					url: `/pages/order/createOrder?data=${JSON.stringify({
						goodsData: goodsData
					})}`
				})
				this.$api.msg('跳转下一页 sendData');
			}
		}
	}
</script>

<style lang="scss">
.empty{
	width: 100%;
	height: 100vh;
	
	display:flex;
	justify-content: center;
	flex-direction: column;
	align-items:center;
	background: #fff;
	
	image{
		width: 240upx;
		height: 160upx;
		margin-bottom:30upx;
	}
	.empty-tips{
		display:flex;
		font-size: $font-sm+2upx;
		color: $font-color-disabled;
		
		.navigator{
			color: $base-color;
		}
	}
}
.cart-list{
		display: flex;
		flex-direction: column;
		.cart-item{
			padding:30upx 40upx;
			display: flex;
			flex-direction: row;
			position: relative;
			.image-wrapper{
				width: 230upx;
				height: 230upx;
				position: relative;
				flex-shrink: 0;
				image{
					width: 100%;
					height: 100%;
					border-radius: 8upx;
				}
				.checkbox{
					position: absolute;
					left:-16upx;
					top: -16upx;
					z-index: 8;
					font-size: 44upx;
					line-height: 1;
					padding: 4upx;
					color: $font-color-disabled;
					background:#fff;
					border-radius: 50px;
				}
				.checked{
					color: $uni-color-primary;
				}
			}
			.item-right{
				display: flex;
				flex-direction: column;
				overflow: hidden;
				flex: 1;
				padding-left: 30upx;
				position: relative;
				.title,.price{
					font-size:$font-base + 2upx;
					color: $font-color-dark;
					height: 40upx;
					line-height: 40upx;
				}
				.attr{
					font-size: $font-sm + 2upx;
					color: $font-color-light;
					height: 50upx;
					line-height: 50upx;
				}
				.price{
					height: 50upx;
					line-height:50upx;
				}
			}
			.del-btn{
				position: absolute;
				top: 30upx;
				right: 40upx;
				font-size:34upx; 
				height: 50upx;
				color: $font-color-light;
			}
		}
	}
	
.action-section{
	display: flex;
	flex-direction: row;
	background: white;
	padding: 10upx 30upx;
	width: 670upx;
	margin-left: 40upx;

	position: fixed;
	box-shadow: 0 0 20upx 0 rgba(0,0,0,.5);
	border-radius: 16upx;
	left: 0;
	bottom:  20upx;
	opacity: 0.9;
	justify-content: center;
	align-items: center;
	.checkbox{
		position: relative;
		image{
			z-index: 10;
			border-radius: 50upx;
			width: 40upx;
			height: 40upx;
			position: absolute;
			left: 0;
			bottom: 0;
		}
		.clear-btn{
			background: lightgray;
			border-radius: 20upx;
			font-size: 26upx;
			color: white;
			width: 120upx;
			height: 40upx;
			line-height: 40upx;
			text-align: right;
			padding-right: 20upx;
			transition: .2s;
			opacity: 0;
			&.show{
				opacity: 1;
				width: 120upx;
			}
		}
	}
	.total-box{
		display: flex;
		flex-direction: column;
		flex: 1;
		text-align: right;
		margin-right: 30upx;
		.price{
			color: #333;
			font-size: 28upx;
		}
		.coupon{
			color: lightgray;
			font-size: 26upx;
			display: flex;
			flex-direction: row;
			padding-left: 140upx;
			/* 不知道为啥 这个属性无效 */
			text-align: right;
			text{
				color: #333;
			}
		}
	}
	.confirm-btn{
		width: 150upx;
		background: $uni-color-primary;
		border-radius: 50upx;
		color: white;
		font-size: 28upx;
		box-shadow: 1px 2px 5px rgba(217, 60, 93, 0.72)
	}
}
</style>
