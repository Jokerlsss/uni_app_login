<template>
	<view class="container" :style="(ispdFocus || isuserFocus)?containerHeight.focus:containerHeight.blur">
		<view class="input-area">
			<view class="user-icon">
				<image :src="userImg" :style="isDisabledBtn?'opacity:0.5':'opacity:1'"></image>
			</view>
			<!-- 用户名输入框 -->
			<view class="input-text" :style="isuserFocus?input_boder_style.focus:input_boder_style.blur">
				<view :style="isuserFocus?label_style.focus:label_style.blur">
					帐 号
				</view>

				<u-input :focus="un" v-model="userLoginInfo.userName" type="text" :height="100" placeholder="" :custom-style="customStyle"
				 :clearable="false" @focus="userFocus" @blur="userBlur" />

				<view class="clear" @click="clearInput('userName')" v-show="userLoginInfo.userName!=='' && isuserFocus">
					<image :src="clearImg" class="img"></image>
				</view>
			</view>
			<!-- 密码输入框 -->
			<view class="input-text" :style="ispdFocus?input_boder_style.focus:input_boder_style.blur">
				<view :style="ispdFocus?label_style.focus:label_style.blur">
					密 码
				</view>

				<u-input :focus="pd" v-model="userLoginInfo.password" type="password" :password-icon="false" :height="100"
				 placeholder="" :maxlength="pdMaxLength" :custom-style="customStyle" :clearable="false" @focus="pdFocus" @blur="pdBlur" />

				<view class="clear" @click="clearInput('password')" v-show="userLoginInfo.password!=='' && ispdFocus">
					<image :src="clearImg" class="img"></image>
				</view>
			</view>

			<!-- 登录按钮 -->
			<view class="btn">
				<u-button size="default" :loading="isLogining" :ripple="true" :custom-style="isDisabledBtn?login_btn_style.disabled:login_btn_style.able"
				 :disabled="isDisabledBtn" @click="login">{{isLogining?'':'登 录'}}</u-button>
			</view>

			<u-toast ref="uToast"></u-toast>
		</view>
		<view class="copy-right">CopyRight 2020.09.20 FZYK</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// ------------- 其他 -------------
				userLoginInfo: {
					userName: '',
					password: ''
				},

				// 整体容器高度，单位 rpx

				// containerHeight: 1000,
				containerHeight: {
					focus: 'height:800rpx;transition:0.2s',
					blur: 'height:1300rpx;transition:0.2s'
				},

				// 获取焦点时，整体上移的动画效果
				animationData: {},

				// 登录界面的用户图标
				userImg: '../../static/img/user.png',

				// 是否正在清除
				isClearing: false,

				// ------------- 输入框 -------------
				pdMaxLength: 10,
				// 输入框是否获取到焦点
				isuserFocus: false,
				ispdFocus: false,

				// 输入框自定义样式
				customStyle: {
					"padding-left": "40rpx",
					// 使光标消失（iOS无效果）
					"color": "transparent",
					"text-shadow": "0 0 0 #000"
				},

				// 清除按钮图标
				clearImg: '../../static/img/clear.png',

				// 输入框 label 样式
				label_style: {
					focus: 'width:100rpx;display:flex;justify-content:center;color:#FF5242;font-weight:bold;transform:scale(1.1)',
					blur: 'width:100rpx;display:flex;justify-content:center;color:#aaaaaa;transform:scale(1)'
				},

				input_boder_style: {
					focus: 'border-bottom: 1px solid #FF5242;',
					blur: 'border-bottom: 1px solid #e4e4e4;'
				},

				// ------------- 登录按钮 -------------
				// 登录按钮自定义样式
				login_btn_style: {
					able: {
						"width": "100%",
						"height": "100rpx",
						"border-radius": "20rpx",
						"border": "#e4e4e4",
						"background-color": "#FF5242",
						"color": "#fff"
					},
					disabled: {
						"width": "100%",
						"height": "100rpx",
						"border-radius": "20rpx",
						"border": "#e4e4e4",
						"background-color": "#FF5242",
						"color": "#fff",
						"opacity": "0.5"
					}
				},

				// 点击登录按钮后，接口返回数据前，对该操作上锁
				isLogining: false,

				un: false,
				pd: false,

				isDisabledBtn: true
			}
		},
		methods: {
			userFocus() {
				// 是否在焦点上
				this.isuserFocus = true
			},
			userBlur() {
				setTimeout(() => {
					this.isuserFocus = false
				}, 1)
			},

			pdFocus() {
				this.ispdFocus = true
			},
			pdBlur() {
				// 失去焦点事件先于清除事件触发，因此让其延迟即可先触发 clearInput 事件
				setTimeout(() => {
					this.ispdFocus = false
				}, 1)
			},

			// 清除 input 内容
			clearInput(value) {
				switch (value) {
					case 'userName':
						setTimeout(() => {
							this.userLoginInfo.userName = ''
							// 清空内容之后保持焦点
							this.un = false
							this.$nextTick(() => {
								this.un = true
							})
						}, 2)
						break
					case 'password':
						setTimeout(() => {
							this.userLoginInfo.password = ''
							this.pd = false
							this.$nextTick(() => {
								this.pd = true
							})
						}, 2)
						break
				}
			},

			login() {
				// 密码正确
				this.isLogining = false
				this.$refs.uToast.show({
					title: '登录成功',
					type: 'success'
				})
			}
		},
		watch: {
			userLoginInfo: {
				handler(newVal, oldVal) {
					if ((newVal.userName !== '') && (newVal.password !== '')) {
						this.isDisabledBtn = false
					} else {
						this.isDisabledBtn = true
					}
				},
				deep: true
			}
		}
	}
</script>

<style lang="scss">
	$screen-height:1334rpx;

	.container {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		align-items: flex-end;
		width: 100%;


		.input-area {
			width: 80%;

			.user-icon {
				width: 100%;
				display: flex;
				justify-content: center;

				>image {
					width: 150rpx;
					height: 150rpx;
				}
			}

			.input-text {
				display: flex;
				flex-wrap: nowrap;
				align-items: center;
				margin-top: 20rpx;

				.clear {
					height: 100rpx;
					display: flex;
					align-items: center;

					.img {
						height: 36rpx;
						width: 36rpx;
					}
				}
			}

			.btn {
				margin-top: 40rpx;

				.login-btn {
					width: 100%;
					border-radius: 20rpx;
					border: $uni-color-primary;
					background-color: $uni-color-primary;
					color: $uni-text-color-inverse;
				}
			}
		}

		.copy-right {
			// bottom: 100rpx;
			bottom: 0;
			width: 100%;
			color: $uni-text-color-grey;
			text-align: center;
			font-size: 24rpx;
		}
	}
</style>
