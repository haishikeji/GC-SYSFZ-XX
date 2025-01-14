<template>
	<view class="content">
		<view class="title">一、公共部分信息</view>
		<u-cell-group :customStyle="{'width': '100vw', 'background-color': '#fff'}">
			<u-cell title="检测项目" titleStyle="width:20vw;" :value="checkItem"></u-cell>
			<u-cell title="检测依据" titleStyle="width:20vw;" :value="checkBy"></u-cell>
			<u-cell title="任务号" titleStyle="width:20vw;" :value="taskNo"></u-cell>
			<u-cell title="量具编号" titleStyle="width:20vw;" :value="measureToolNo"></u-cell>
		</u-cell-group>
		<view class="title">二、称样信息</view>
		<view class="empty" v-if="!samples.length">
			<u--image :showLoading="true" src="../../static/images/empty.png" width="120px" height="120px" @click="click"></u--image>
			<text class="text">暂无数据</text>
		</view>
		<block v-for="item, index in samples" key="item.id">
			<view :class="((index%2) == 0) ? 'card white' : 'card black'">
				<view class="name">{{item.sampleName}}</view>
				<view class="value">
					<text>称样量:</text>
					<u--input class="num" v-model="item.weighSampleNum" border="surround" @blur="saveData(index,item.id)"></u--input>
					<u-button text="g" type="primary" class="custom-style" :class="(item.weighUnit == 'G') ? 'selected' : 'unselected'" size="mini" @click="units(index,1,item.id)"></u-button>
					<u-button text="ml" type="primary" class="custom-style" :class="(item.weighUnit == 'ML') ? 'selected' : 'unselected'" size="mini" @click="units(index,2,item.id)"></u-button>
				</view>
			</view>
		</block>
		<u-button class="submit" type="primary" @click="submit">提交</u-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 称样单id
				id: 0,
				// 检测依据
				checkBy: '',
				// 检测项目
				checkItem: '',
				// 量具编号
				measureToolNo: '',
				// 任务号
				taskNo: '',
				samples: []
			}
		},
		onLoad(options) {
			this.id = options.id
			this.getData()
		},
		methods: {
			units(index,id,ids){
				// console.log(index,id,ids)
				if(id == 1){
					this.samples[index].weighUnit = 'G'
					this.saveData(index,ids)
				}else{
					this.samples[index].weighUnit = 'ML'
					this.saveData(index,ids)
				}
			},
			// 获取信息
			getData(){
				let params = new Object();
				params.id = this.id;
				this.$request.get('/h5/weigh_sample_order/details', params).then(res => {
					this.checkBy = res.checkBy;
					this.checkItem = res.checkItem;
					this.measureToolNo = res.measureToolNo;
					this.taskNo = res.taskNo;
					this.samples = res.samples;
				})
			},
			// 保存信息（单个）
			saveData(index,id){
				console.log(index,id,this.samples[index].weighUnit)
				if(this.samples[index].weighSampleNum == ''){
					uni.$u.toast('请输入称样量')
					return
				}
				if(this.samples[index].weighUnit == ''){
					uni.$u.toast('请选择称样量单位')
					return
				}
				let str = this.samples[index].weighSampleNum.split(".")
				if((str[0].length < 1) || (str[0].length > 2)){
					uni.$u.toast('整数长度1~2位')
					return
				}
				if(str[1].length != 4){
					uni.$u.toast('小数点后长度4位')
					return
				}
				let params = new Object();
				params.id = id;
				params.weighUnit = this.samples[index].weighUnit;
				params.weighSampleNum = this.samples[index].weighSampleNum;
				this.$request.post('/h5/weigh_sample_order/save', params).then(res => {
					uni.$u.toast('样品' + this.samples[index].sampleName + '保存成功')
				})
			},
			// 提交
			submit(){
				let params = new Object();
				params.id = this.id;
				this.$request.post('/h5/weigh_sample_order/submit', params).then(res => {
					uni.$u.toast('提交成功')
					setTimeout(() => {
						uni.switchTab({
							url: '/pages/list/list'
						})
						clearTimeout()
					},1000)
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.card{
		width: 96vw;
		height: 24vw;
		margin: 2vw 2vw 0;
		border-radius: 1vw;
		background-color: #bbb;
	}
	.white{
		background-color: #ffffff;
	}
	.black{
		background-color: #efefef;
	}
	.name{
		margin: 3vw;
	}
	.submit{
		width: 96vw;
		margin: 10vw 2vw;
	}
	.value{
		display: flex;
		align-items: center;
		margin: 0 3vw;
		.num{
			font-size: 4.5vw;
			font-weight: 600;
			margin-left: 4vw;
		}
	}
	.selected{
		border: 1px solid #3c9cff;
		background-color: #3c9cff;
	}
	.unselected{
		border: 1px solid #a1caff;
		background-color: #a1caff;
	}
	.custom-style{
		width: 10vw;
		margin-left: 2vw;
	}
	.empty{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: #a2a2a2;
		margin: 20vw 0;
		.text{
			margin-top: 5vw;
			font-size: 4.5vw;
		}
	}
</style>
