# z-paging-x
> z-paging uniapp x版

[![version](https://img.shields.io/badge/version-0.1.0-blue)](https://github.com/SmileZXLee/uni-z-paging-x)
[![license](https://img.shields.io/github/license/SmileZXLee/uni-z-paging-x)](https://en.wikipedia.org/wiki/MIT_License)

***

### 反馈qq群(点击加群)：[371624008](http://qm.qq.com/cgi-bin/qm/qr?_wv=1027&k=avPmibADf2TNi4LxkIwjCE5vbfXpa-r1&authKey=dQ%2FVDAR87ONxI4b32Py%2BvmXbhnopjHN7%2FJPtdsqJdsCPFZB6zDQ17L06Uh0kITUZ&noverify=0&group_code=371624008)

***

## z-paging-x文档

### 安装

#### 方式1(推荐)：通过uni_modules安装，在插件市场中点击右上角【使用HbuilderX导入插件】即可。 

***

#### 方式2：通过npm安装  

```bash
//若项目之前未使用npm管理依赖（项目根目录下无package.json文件），先在项目根目录执行命令初始化npm工程
npm init -y

//安装
npm install z-paging-x --save
//更新
npm update z-paging-x
```

### 基本使用

```html
<template>
	<z-paging-x ref="pagingX" v-model="dataList" @query="queryList">
		<template #top>
			<common-tabs :list="tabList" @change="tabChange" />
		</template>
		<list-item v-for="(item, index) in dataList" :key="index">
			<view class="list-item">
				<text class="list-item-title">{{item.title}}</text>
				<text class="list-item-type">{{item.detail}}</text>
			</view>
		</list-item>
	</z-paging-x>
</template>

<script lang="uts">
	import { requestList, ListItem } from '@/http/request.uts'
	export default {
		data() {
			return {
				tabList: ['测试1','测试2','测试3','测试4'] as Array<string>,
				tabIndex: 0,
				dataList: [] as Array<ListItem>
			}
		},
		methods: {
			tabChange(index: number) {
				this.tabIndex = index;
				// 刷新列表数据
				(this.$refs['pagingX'] as ZPagingXComponentPublicInstance).reload();
			},
			// @query所绑定的方法不要自己调用！！需要刷新列表数据时，只需要调用(this.$refs['pagingX'] as ZPagingXComponentPublicInstance).reload()即可
			queryList(pageNo: number, pageSize: number) {
				const params = {
					pageNo: pageNo,
					pageSize: pageSize,
					type: this.tabIndex + 1
				}
				// 此处请求仅为演示，请替换为自己项目中的请求
				requestList(params).then((res: UTSJSONObject) => {
					// 将请求结果通过complete传给z-paging处理，同时也代表请求结束，这一行必须调用
					(this.$refs['pagingX'] as ZPagingXComponentPublicInstance).complete(res['data'] as Array<any>);
				})
			}
		}
	}
</script>
```

### props

| 参数                 | 说明                                                         | 类型    | 默认值 | 可选值 |
| :------------------- | :----------------------------------------------------------- | :------ | :----- | :----- |
| v-model              | 绑定最终的列表渲染变量(页面data中声明的值)，当列表数据改变时，所绑定的变量会跟着改变 | Array   | -      | -      |
| default-page-no      | 自定义初始的pageNo(从第几页开始)                             | Number  | 1      | -      |
| default-page-size    | 自定义pageSize(每页显示多少条)                               | Number  | 10     | -      |
| fixed                | z-paging-x是否使用fixed布局，若使用fixed布局，则z-paging-x的父view无需固定高度，z-paging-x高度默认铺满屏幕，页面中的view请放在z-paging-x标签内，需要固定在顶部的view使用`slot="top"`包住，需要固定在底部的view使用`slot="bottom"`包住。 | Boolean | true   | false  |
| auto                 | mounted后自动调用reload方法(mounted后自动调用接口)           | Boolean | true   | false  |
| refresher-enabled    | 是否开启自定义下拉刷新                                       | Boolean | true   | false  |
| refresher-threshold  | 设置自定义下拉刷新阈值，默认单位为px。默认高度等于refresher高度 | Number  | 0      | -      |
| use-custom-refresher | 是否使用自定义的下拉刷新，默认为是，即使用z-paging-x的下拉刷新。设置为false即代表使用unix自带的下拉刷新 | Boolean | true   | false  |
| load-more-enabled    | 是否启用加载更多数据(含滑动到底部加载更多数据和点击加载更多数据) | Boolean | true   | false  |


### events

| 事件名 | 说明                                                         | 回调参数                                                     |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| @query | 下拉刷新或滚动到底部时会自动触发此方法。`z-paging-x`加载时也会触发(若要禁止，请设置`:auto="false"`)。pageNo和pageSize会自动计算好，直接传给服务器即可。 | `参数1`:pageNo(当前第几页)；<br/>`参数2`:pageSize(每页多少条)(pageSize必须与传给服务器的一致，如果需要修改pageSize，请通过`:default-page-size="15"`修改) |

### methods

| 方法名   | 说明                                                         | 参数                         |
| -------- | ------------------------------------------------------------ | ---------------------------- |
| reload   | 重新加载分页数据，pageNo恢复为默认值，相当于下拉刷新的效果   | -                            |
| complete | 请求结束(成功或者失败)调用此方法，将请求的结果传递给`z-paging-x`处理，会自动判断是否有更多数据(当通过`complete`传进去的数组长度小于`pageSize`时，则判定为没有更多了)。 | `参数1(必填)`:请求结果数组； |

### slots

| 名称      | 说明                                                         |
| :-------- | ------------------------------------------------------------ |
| top       | 可以将自定义导航栏、tab-view等需要固定的`(不需要跟着滚动的)`元素放入`slot="top"`的view中。<br/>注意，当有多个需要固定的view时，请用一个view包住它们，并且在这个view上设置`slot="top"`。需要固定在顶部的view请勿设置`position: fixed;` |
| bottom    | 可以将需要固定在底部的`(不需要跟着滚动的)`元素放入`slot="bottom"`的view中。<br/>注意，当有多个需要固定的view时，请用一个view包住它们，并且在这个view上设置`slot="bottom"`。需要固定在底部的view请勿设置`position: fixed;`。 |
| refresher | 自定义下拉刷新view，设置后则不使用uni自带的下拉刷新view和z-paging自定义的下拉刷新view。<br>slot-scope="{ refresherStatus(0-默认状态 1.松手立即刷新 2.刷新中 3.刷新成功) }" |