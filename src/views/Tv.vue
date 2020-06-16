<template>
	<el-row :gutter="10">
		<el-col
			:xs="{ span: 24, offset: 0 }"
			:sm="{ span: 20, offset: 2 }"
			:md="{ span: 16, offset: 4 }"
			><div class="main-box">
				<!-- title -->
				<el-menu mode="horizontal" class="title-box">
					<el-menu-item>视频解析</el-menu-item>
				</el-menu>

				<div class="media-box">
					<!-- iframe -->
					<div class="">
						<iframe
							allowfullscreen="true"
							class="iframe-item "
							:src="fullUrl"
							frameborder="0"
						></iframe>
					</div>

					<!-- form -->
					<el-form
						:inline="true"
						:model="formValue"
						class="form-box media-box"
						ref="formValue"
						:rules="rules"
					>
						<el-form-item>
							<el-select v-model="formValue.line" placeholder="选择线路">
								<el-option label="线路一🔥" value="1"></el-option>
								<el-option label="线路二" value="2"></el-option>
								<el-option label="线路三" value="3"></el-option>
							</el-select>
						</el-form-item>
						<el-form-item prop="url">
							<el-input
								v-model="formValue.url"
								placeholder="视频地址"
							></el-input>
						</el-form-item>
						<el-form-item>
							<el-button type="primary" @click="submitForm('formValue')"
								>开始播放</el-button
							>
						</el-form-item>
					</el-form>

					<!-- history -->
					<el-card class="media-box history-box">
						<div slot="header" class="">
							<span>历史记录</span>
						</div>
						<div v-for="item in histories" :key="item" class="">
							<p>{{ item.title }}</p>
							<p>{{ item.url }}</p>
						</div>
					</el-card>
					<!-- footer -->
				</div>
			</div></el-col
		>
	</el-row>
</template>

<script>
const xuanlu = {
	'1': 'http://jqaaa.com/jx.php?url=',
	'2': 'https://api.spjx.live/?url=',
	'3': 'http://j.zz22x.com/jx/?url='
}

export default {
	name: 'Tv',
	data() {
		return {
			formValue: {
				line: '1',
				url: ''
			},
			rules: {
				url: [{ required: true, message: '请输入视频地址', trigger: 'blur' }]
			},
			fullUrl: xuanlu['1'],

			histories: []
		}
	},
	methods: {
		// 开始播放
		submitForm(formName) {
			this.$refs[formName].validate((valid) => {
				if (valid) {
					this.fullUrl = xuanlu[this.formValue.line] + this.formValue.url

					this.setHistory(this.formValue.url)

					this.$message({
						message: '开始播放',
						type: 'success'
					})
				} else {
					console.log('error submit!!')
					return false
				}
			})
		},
		// 设置历史记录
		setHistory(url) {},

		// 获取title
		getTitle() {
			let _this = this
			return new Promise((reslove, reject) => {
				$.ajax({
					url: 'http://tvprase.seast.net/', //腾讯云函数
					// url: 'https://1116304423746548.cn-hangzhou.fc.aliyuncs.com/2016-08-15/proxy/webTools/get-video-site-title/',
					data: {
						url: _this.inputUrl
					},
					dataType: 'json',
					success(res) {
						// console.log(res);
						reslove(res.title)
					},
					error(res) {
						console.log(res)
						reslove('无')
					}
				})
			})
		}
	},
	mounted: {}
}
</script>

<style scoped>
.title-box {
	margin-bottom: 10px;
}

.main-box {
	text-align: center;
	text-align: -moz-center;
}

.form-box {
	text-align: center;
	margin-top: 20px;
}

.history-box {
	text-align: left;
}

@media screen and (max-width: 768px) {
	.media-box {
		width: 100%;
	}
	.iframe-item {
		height: 220px;
		width: 100%;
	}
}

@media screen and (min-width: 768px) and (max-width: 992px) {
	.media-box {
		width: 610px;
	}

	.iframe-item {
		height: 343px;
		width: 100%;
	}
}

@media screen and (min-width: 992px) {
	.media-box {
		width: 730px;
	}

	.iframe-item {
		height: 411px;
		width: 100%;
	}
}
</style>
