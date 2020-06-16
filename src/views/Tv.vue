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

				<div class="media-box ">
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
								v-model.trim="formValue.url"
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
						<div v-for="(item, index) in histories" :key="index">
							<p>{{ item.title }}</p>
							<p>
								{{ item.url }}
								<el-tooltip
									class="item"
									effect="dark"
									content="点击复制到输入框"
									placement="top-start"
								>
									<i
										class="el-icon-document-copy icon-copy"
										@click="onCopy(item.url)"
									></i>
								</el-tooltip>
							</p>
							<el-divider></el-divider>
						</div>
					</el-card>
					<!-- footer -->
					<p style="color:#C0C4CC;text-align: center">Copyright © 2020 桑易</p>
				</div>
			</div></el-col
		>
	</el-row>
</template>

<script>
// 解析地址
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

			fullUrl: xuanlu['1'], //拼接后的完整地址

			histories: [] //历史记录
		}
	},
	methods: {
		// 开始播放
		submitForm(formName) {
			this.$refs[formName].validate((valid) => {
				if (valid) {
					this.fullUrl = xuanlu[this.formValue.line] + this.formValue.url

					this.$message({
						message: '开始播放',
						type: 'success'
					})

					this.setHistory(this.formValue.url)
				}
			})
		},

		// 获取历史记录
		getHistories() {
			this.histories = JSON.parse(localStorage.getItem('histories')) || []
		},

		// 设置历史记录
		async setHistory(url) {
			let histories = JSON.parse(localStorage.getItem('histories')) || []

			// 最近一条记录重复则不添加
			if (histories[0] && histories[0].url === url) {
				return
			}

			try {
				let data = await this.getTitle(url)
				histories.unshift({
					title: data.data.title,
					url: url
				})
			} catch {
				histories.unshift({
					title: '?_?',
					url: url
				})
			}

			histories = histories.slice(0, 3)

			localStorage.setItem('histories', JSON.stringify(histories))

			this.getHistories()
		},

		// 获取title
		getTitle(url) {
			return this.$axios({
				method: 'get',
				url:
					'https://service-4yrjp0u8-1254302252.gz.apigw.tencentcs.com/release/tv-prase',
				params: {
					url: url
				}
			})
		},

		// 复制url到input
		onCopy(url) {
			this.formValue.url = url
			this.$message({
				message: '已复制地址到输入框',
				type: 'success'
			})
		}
	},
	mounted() {
		// this.setHistory('https://v.qq.com/x/cover/mzc00200rxjna4e.html')
		this.getHistories()
	}
}
</script>

<style scoped>
.title-box {
	margin-bottom: 10px;
}

.form-box {
	text-align: center;
	margin-top: 20px;
}

.history-box {
	text-align: left;
}

.history-box p {
	margin: 0;
	line-height: 24px;
}

.history-box p:nth-child(2) {
	color: #606266;
}

.el-divider {
	margin-top: 8px;
	margin-bottom: 8px;
}

.icon-copy {
	color: #409eff;
}

.media-box {
	margin-left: auto;
	margin-right: auto;
}

@media screen and (max-width: 768px) {
	.media-box {
		width: 100%;
	}
	.iframe-item {
		height: 232px;
		width: 100%;
	}
}

@media screen and (min-width: 768px) and (max-width: 992px) {
	.media-box {
		width: 610px;
	}

	.iframe-item {
		height: 373px;
		width: 100%;
	}
}

@media screen and (min-width: 992px) {
	.media-box {
		width: 730px;
	}

	.iframe-item {
		height: 440px;
		width: 100%;
	}
}
</style>
