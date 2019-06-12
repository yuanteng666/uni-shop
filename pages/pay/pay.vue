<template>
	<view>
		<view class="price-box">
			<text>支付金额</text>
			<text class="price">38.88</text>
		</view>
		
		<view class="pay-type-list">
		
			<view class="type-item b-b" @click="changePayType(1)">
				<text class="icon yticon icon-weixinzhifu"></text>
				<view class="con">
					<text class="tit">微信支付</text>
					<text>推荐使用微信支付</text>
				</view>
				<label class="radio">
					<radio value="" color="#fa436a" :checked='payType == 1' />
					</radio>
				</label>
			</view>
			<view class="type-item b-b" @click="changePayType(2)">
				<text class="icon yticon icon-alipay"></text>
				<view class="con">
					<text class="tit">支付宝支付</text>
				</view>
				<label class="radio">
					<radio value="" color="#fa436a" :checked='payType == 2' />
					</radio>
				</label>
			</view>
			<view class="type-item" @click="changePayType(3)">
				<text class="icon yticon icon-erjiye-yucunkuan"></text>
				<view class="con">
					<text class="tit">预存款支付</text>
					<text>可用余额 ¥198.5</text>
				</view>
				<label class="radio">
					<radio value="" color="#fa436a" :checked='payType == 3' />
					</radio>
				</label>
			</view>
		</view>
		
		<text class="mix-btn" @click="confirm">确认支付</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				payType: 1,
				orderInfo: {}
			}
		},
		methods: {
			//选择支付方式
			changePayType(type) {
				this.payType = type;
			},
			//确认支付
			confirm: async function() {
				uni.redirectTo({
					url: '/pages/paysuccess/paysuccess'
				})
			},
		}
	}
</script>

<style lang="scss">
.price-box{
	color: #888888;
	font-size: $font-base;
	display: flex;
	flex-direction: column;
	text-align: center;
	justify-content: center;
	align-items: center;
	height: 265upx;
	width: 100%;
	.price{
		color: black;
		margin-top: 10upx;
		font-size: $font-lg + 4upx;
		&:before{
			content: '￥';
			font-size: $font-lg + 4upx;
		}
	}
}
.pay-type-list{
	display: flex;
	flex-direction: column;
	padding-left: 40upx;
	padding-right: 40upx;
	.type-item{
		height: 120upx;
		align-items: center;
		display: flex;
		flex-direction: row;
		.icon{
			width: 100upx;
			font-size: 52upx;
		} 
		.icon-erjiye-yucunkuan {
			color: #fe8e2e;
		}
		.icon-weixinzhifu {
			color: #36cb59;
		}
		.icon-alipay {
			color: #01aaef;
		}
		.con{
			display: flex;
			flex-direction: column;

			color: #888888;
			font-size: $font-base;
		}
		.tit{
			color: #333333;
			font-size: $font-lg;
		}
		.radio{
			flex: 1;
			text-align: right;
		}
	}
	.b-b{
		border-bottom: 1upx solid #c1c1c1;
	}
}

.mix-btn{
	margin: 40upx;
	color: white;
	display: block;
	padding: 10upx;
	margin-top: 100upx;
	background: $uni-color-primary;
	text-align: center;
	height: 90upx;
	line-height: 90upx;
	border-radius: 10upx;
}
</style>
