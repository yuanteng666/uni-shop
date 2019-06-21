<template>
	<view class="content">
		<scroll-view scroll-y class="left-aside">
			<view v-for="(item,index) in flist" :key='item.id' class="f-item b-b" :class="{active: item.id === currentId}" @click="tabtap(item)">				
				{{item.name}}
			</view>
		</scroll-view>
		<scroll-view scroll-y scroll-with-animation class="right-aside" @scroll="asideScroll" :scroll-top="tabScrollTop">
			<view class="s-list" v-for="item in slist" :key='item.id' :id="'main-'+item.id">
				<text class="s-item">{{item.name}}</text>
				<view class="t-list">
					<view v-for="titem in tlist" v-if="titem.pid === item.id" class="t-item" :key="titem.id"  @click="navToList(item.id, titem.id)">
						<image :src="titem.picture"></image>
						<text>{{titem.name}}</text>
					</view>
				</view>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				sizeCalcState: false,
				tabScrollTop: 0,
				currentId: 1,
				flist: [],
				slist: [],
				tlist: [],
			}
		},
		onLoad() {
			this.loadData()
		},
		methods: {
			async loadData(){
				let list = await this.$api.json('cateList');
				list.forEach(item=>{
					if(!item.pid){
						this.flist.push(item);  //pid为父级id, 没有pid或者pid=0是一级分类
					}else if(!item.picture){
						this.slist.push(item); //没有图的是2级分类
					}else{
						this.tlist.push(item); //3级分类
					}
				}) 
			},
			//一级分类点击
			tabtap(item){
				if(!this.sizeCalcState){
					this.calcSize();
				}
				
				this.currentId = item.id;
				let index = this.slist.findIndex(sitem=>sitem.pid === item.id);
				this.tabScrollTop = this.slist[index].top;
			},
			//右侧栏滚动
			asideScroll(e){
				if(!this.sizeCalcState){
					this.calcSize();
				}
				let scrollTop = e.detail.scrollTop;
				console.log("e.detail--->"+scrollTop)
				//item top 小于scrollTop的有多个item ，翻转filter后的数组，根据翻转后的数组第一个判断左侧该选中哪一个
				let tabs = this.slist.filter(item=>item.top <= scrollTop).reverse();
				
				if(tabs.length > 0){
					this.currentId = tabs[0].pid;
				}
			},
			//计算右侧栏每个tab的高度等信息
			calcSize(){
				
				let h = 0;
				this.slist.forEach(item=>{
					let view = uni.createSelectorQuery().select("#main-" + item.id);
					view.fields({
						size: true
					}, data => {
						item.top = h;
						h += data.height;
						item.bottom = h;
						console.log('item top'+item.top)
					}).exec();
				})
				this.sizeCalcState = true;
			},
			navToList(sid, tid){
				uni.navigateTo({
					url: `/pages/produceList/produceList?fid=${this.currentId}&sid=${sid}&tid=${tid}`
				})
			}
		}
	}
</script>

<style lang="scss">
page,
.content {
	height: 100%;
	background-color: #f8f8f8;
}
.content{
	display: flex;
}
.left-aside{
	flex-shrink: 0;
	width:200upx;	
	height: 100%;
	background: white;

	.f-item{
		width: 100%;
		height: 100upx;
		line-height: 100upx;
		text-align: center;
		font-size: 28upx;
		color: $font-color-base;
		&.active{
			position: relative;
			color: $base-color;
			background: #f8f8f8;
			&:before{
				content: '';
				position: absolute;
				left: 0;
				top: 50%;
				transform: translateY(-50%);
				height: 36upx;
				width: 8upx;
				background-color: $base-color;
				border-radius: 0 4px 4px 0;
				opacity: .8;
			}
		}
	}
}
.b-b{
	border-bottom: 1upx solid #f8f8f8;
}
.right-aside{
	flex: 1;
	margin-left: 20upx;
	
	.s-list{
		display: flex;
		flex-direction: column;
		
		.s-item{
			font-size: 28upx;
			color: #333333;
			padding-bottom: 20upx;
			padding-top: 20upx;
		}
		.t-list{
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			background: white;
		}
		.t-item{
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			width: 176upx;
			font-size: 26upx;
			color: #666;
			image{
				width: 140upx;
				height: 140upx;
			}
			
		}
	}
}
</style>
