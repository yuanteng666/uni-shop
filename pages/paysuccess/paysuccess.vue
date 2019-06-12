<template>
	<view class="content">
		<text class="success-icon yticon icon-xuanzhong2"></text>
		<text class="tit">支付成功</text>
		
		<view class="btn-group">
			<navigator url="/pages/order/order?state=0" open-type="redirect" class="mix-btn">查看订单</navigator>
			<navigator url="/pages/index/index" open-type="switchTab" class="mix-btn hollow">返回首页</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style lang="scss">
.content{
	display: flex;
	flex-direction: column;
	align-items: center;
	
}
.success-icon{
	margin-top: 100upx;
	width: 160upx;
	font-size: 160upx;
	color:$uni-color-primary ;
}
.tit{
	font-size: 38upx;
	color: #333333;
}
.mix-btn{
	margin: 40upx;
	width: 600upx;
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

.hollow{
	margin-top: 20upx;
	background: white;
	border: 1upx solid #888888;
	color: #333333;
}
</style>
