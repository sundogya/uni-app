<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view>
			<text class="title">{{title}}</text>
		</view>
		<view class="bluetooth">
			<text @click="searchBle">搜索蓝牙设备</text>
		</view>
	</view>
</template>

<script>
	function ab2hex(buffer) {
		let hexArr = Array.prototype.map.call(
			new Uint8Array(buffer),
			function(bit) {
				return ('00' + bit.toString(16)).slice(-2)
			}
		)
		return hexCharCodeToStr(hexArr.join(''));
	}

	function ab2str(buffer) {
		// return String.fromCharCode.apply(null, new Uint8Array(buffer));
	}

	function hexCharCodeToStr(hexCharCodeStr) {
		var trimedStr = hexCharCodeStr.trim();
		var rawStr =
			trimedStr.substr(0, 2).toLowerCase() === "0x" ?
			trimedStr.substr(2) :
			trimedStr;
		var len = rawStr.length;
		if (len % 2 !== 0) {
			alert("Illegal Format ASCII Code!");
			return "";
		}
		var curCharCode;
		var resultStr = [];
		for (var i = 0; i < len; i = i + 2) {
			curCharCode = parseInt(rawStr.substr(i, 2), 16); // ASCII Code Value
			resultStr.push(String.fromCharCode(curCharCode));
		}
		return resultStr.join("");
	}
	export default {
		data() {
			return {
				title: 'Hello',
				isOpenBle: false
			}
		},
		onLoad() {},
		mounted() {},
		methods: {
			searchBle() {
				uni.openBluetoothAdapter({
					success(res) {
						console.log(res)
						setTimeout(() => {
							uni.startBluetoothDevicesDiscovery({
								success(res) {
									console.log(res)
									uni.onBluetoothDeviceFound((res) => {
										if(res.devices[0].name){
											console.log(res)
										}
										uni.createBLEConnection({
											deviceId:'DA:66:0B:1B:86:4C',
											success(res) {
												console.log(res)
												uni.stopBluetoothDevicesDiscovery()
												uni.getBLEDeviceServices({
													deviceId:'DA:66:0B:1B:86:4C',
													success(res) {
														console.log(res)
													}
												})
											}
										})
										// uni.getBluetoothDevices({
										// 	success(res) {
										// 		console.log(res) 
										// 	}
										// })
										// console.log(ab2hex(res.devices[0].advertisData))
									})
								}
							})
						}, 1000)
					}
				})
			}
		}
	}
</script>

<style>
	.content {
		text-align: center;
		height: 400upx;
	}

	.logo {
		height: 200upx;
		width: 200upx;
		margin-top: 200upx;
	}

	.title {
		font-size: 36upx;
		color: #8f8f94;
	}
</style>
