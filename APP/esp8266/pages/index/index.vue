<template>
	<view class="wrap">
		
		<view class="device_title">
			<view class="device_cart">
				<view class="">
					<view class="device_name">温度</view>
					<image class="device_logo" src="../../static/temp.png" mode="">
						</imag>
				</view>
				<view class="device_data"> {{temp}} ℃</view>
			</view>

			<view class="device_cart">
				<view class="">
					<view class="device_name">湿度</view>
					<image class="device_logo" src="../../static/humi.png" mode="">
						</imag>
				</view>
				<view class="device_data"> {{humi}} %</view>
			</view>

			<view class="device_cart">
				<view class="">
					<view class="device_name">台灯</view>
					<image class="device_logo" src="../../static/lamp.png" mode="">
						</imag>
				</view>
				<switch :checked="LED" @change="SendLedStatus" color="#7dc5eb"/>
			</view>
			
			<view class="device_cart">
				<view class="">
					<view class="">待开发···</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	const {
		createCommonToken
	} = require('@/key.js')
	export default {
		data() {
			return {
				temp: '11',
				humi: '22',
				LED: true,
				token: '',
			}
		},
		onLoad() {
			const params = {
				author_key: 'PxaQ9s58f+56L2rG1chTrGg3waPcCOM569Z/UiPpWFsdV48AgSRuN7GiOrR5wQ4bcSOF1wvSbfx07hvmN+RoiA==',
				version: '2022-05-01',
				user_id: '293322',
			}
			this.token = createCommonToken(params);
		},
		onShow() {
			this.fetchDeviceData();
			setInterval(() => {
				this.fetchDeviceData();
			}, 3000)
		},
		methods: {
			//getData
			fetchDeviceData() {
				uni.request({
					url: 'https://iot-api.heclouds.com/thingmodel/query-device-property', //接口地址。
					method: 'GET',
					data: {
						product_id: 'RTHI6U080l',
						device_name: 'mydht11'
					},
					header: {
						'authorization': this.token //自定义请求头信息
					},
					success: (res) => {
						console.log(res.data);
						this.humi = res.data.data[1].value;
						this.temp = res.data.data[2].value;
						this.LED = res.data.data[0].value === 'true' ? true : false;
					}
				});
			},
			//sendData
			SendLedStatus(event) {
				let value = event.detail.value;
				uni.request({
					url: 'https://iot-api.heclouds.com/thingmodel/set-device-property', //接口地址。
					method: 'POST',
					data: {
						product_id: 'RTHI6U080l',
						device_name: 'mydht11',
						params: {
							"LED": value
						}
					},
					header: {
						'authorization': this.token //自定义请求头信息
					},
					success: (res) => {
						console.log('LED' + (value ? 'ON' : 'OFF') + ' !');
					}
				});
			}
		}
	}
</script>

<style>
	.wrap{
		padding: 30rpx;
	}
	.device_title {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.device_logo {
		width: 70rpx;
		height: 70rpx;
		margin-top: 10rpx;
	}

	.device_cart {
		height: 150rpx;
		width: 320rpx;
		border-radius: 30rpx;
		margin-top: 30rpx;
		display: flex;
		justify-content: space-around;
		align-items: center;
		box-shadow: 0 0 15rpx #ccc;
	}

	.device_name {
		font-size: 20rpx;
		color: #6d6d6d;
		text-align: center;
	}

	.device_data {
		font-size: 50rpx;
		color: #6d6d6d;
	}
</style>