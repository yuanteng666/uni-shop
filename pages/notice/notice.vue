<template>
	<view>
		<view class="notice-item">
			<text class="time">11:30</text>
			<view class="content">
				<text class="title">新品上市，全场满199减50</text>
				<view class="img-wrapper">
					<image class="pic" src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1556465765776&di=57bb5ff70dc4f67dcdb856e5d123c9e7&imgtype=0&src=http%3A%2F%2Fimg.zcool.cn%2Fcommunity%2F01fd015aa4d95fa801206d96069229.jpg%401280w_1l_2o_100sh.jpg"></image>
				</view>
				<text class="introduce">
					虽然做了一件好事，但很有可能因此招来他人的无端猜测，例如被质疑是否藏有其他利己动机等，乃至谴责。即便如此，还是要做好事。
				</text>
				<view class="bot b-t">
					<text>查看详情</text>
					<text class="more-icon yticon icon-you"></text>
				</view>
			</view>
		</view>
		<view class="notice-item">
			<text class="time">昨天 12:30</text>
			<view class="content">
				<text class="title">新品上市，全场满199减50</text>
				<view class="img-wrapper">
					<image class="pic" src="https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3761064275,227090144&fm=26&gp=0.jpg"></image>
					<view class="cover">
						活动结束
					</view>
				</view>
				<view class="bot b-t">
					<text>查看详情</text>
					<text class="more-icon yticon icon-you"></text>
				</view>
			</view>
		</view>
		<view class="notice-item">
			<text class="time">2019-07-26 12:30</text>
			<view class="content">
				<text class="title">新品上市，全场满199减50</text>
				<view class="img-wrapper">
					<image class="pic" src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1556465765776&di=57bb5ff70dc4f67dcdb856e5d123c9e7&imgtype=0&src=http%3A%2F%2Fimg.zcool.cn%2Fcommunity%2F01fd015aa4d95fa801206d96069229.jpg%401280w_1l_2o_100sh.jpg"></image>
					<view class="cover">
						活动结束
					</view>
				</view>
				<text class="introduce">新品上市全场2折起，新品上市全场2折起，新品上市全场2折起，新品上市全场2折起，新品上市全场2折起</text>
				<view class="bot b-t">
					<text>查看详情</text>
					<text class="more-icon yticon icon-you"></text>
				</view>
			</view>
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
page{
	background-color: #f7f7f7;
	padding-bottom: 30upx;
}
.notice-item{
	display: flex;
	flex-direction: column;
	padding-left: 20upx;
	padding-right: 20upx;
	.time{
		text-align: center;
		font-size: 26upx;
		color: gray;
		padding-top: 15upx;
		padding-bottom: 15upx;
	}
	.content{
		background: white;
		padding: 20upx;
		.title{
			color: black;
			font-size: 32upx;
		}
	}
	.img-wrapper{
		margin-top: 20upx;
		width: 100%;
		height: 260upx;
		position: relative;
		.pic{
			width: 100%;
			height: 100%;
		}
		.cover{
			width: 100%;
			height: 100%;
			position: absolute;
			left: 0;
			top: 0;
			background: rgba(0,0,0,0.4);
			color: white;
			text-align: center;
			line-height: 260upx;
			font-weight: 100;
		}
	}

	.introduce{
		font-size: 27upx;
		color: gray;
		line-height: 1;
		margin-bottom: 20upx;
	}
	.bot{
		display: flex;
		flex-direction: row;
		color: lightgray;
		font-size: 28upx;
	}
	.b-t{
		border-top: 1upx dashed #ddd;
		padding-top: 20upx;
		margin-top: 20upx;
	}
	.more-icon{
		flex: 1;
		text-align: right;
	}
}
</style>
