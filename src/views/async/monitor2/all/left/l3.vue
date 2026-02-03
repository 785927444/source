<template>
  <div class="layout-col">
    <!-- 标题 -->
		<div class="h40 ww100 flex-sc relative mb5">
			<img class="hh100" src="@/assets/imgs/title.png" />
			<div class="ww100 hh100 flex-sc absolute pl35">
				<span class="fw flex1 ptb5">储能柜充电功率情况</span>
				<div class="flex-ec flex1"></div>
			</div>
		</div>
    <!-- 内容 -->
    <div class="layout-col p12 ba1">
      <div class="hh100 ww100" id="Line_DevicesInputPower"></div>
    </div>
  </div>
</template>

<script lang="ts" setup>
  import ec from "@/utils/chart";
  const { drawChart } = ec();
  const { proxy }: any = getCurrentInstance();
  const publicStore = proxy.publicStore();
  const configStore = proxy.configStore();
  const state = reactive({
    ...publicStore.$state,
  });

	watch(() => publicStore._public.DevicesInputPower, async(val, old) => {
		if(proxy.isNull(val)) return
		if(JSON.stringify(val) == JSON.stringify(old)) return
		await nextTick();
		setChart(val);
	}, { immediate: false, deep: true });

	const setChart = async(data) => {
		let chart = 'Line'
		let id = 'Line_DevicesInputPower'
		let attr = {
			// min: 0, 
			// max: 100,
			extent: 4, 
			colorList: [{color: '#FFC860', start: 'rgba(255, 200, 96, 0.5)', end: 'rgba(255, 200, 96, 0.1)'}, ],
			legendArr: [],
			legendShow: false,
			labelShow: false,
			areaShow: true,
			y_name: 'kW',
			tool_dw: '', 
			tool_name_x: '日期：', 
			tool_name_y: '数值：', 
		}
		let series = {data: []}
		series = data.map(v => {
			attr.legendArr.push(v.name)
			let data = v.data
      if(v.data.length>20) attr.symbol = true
			return {data}
		})
		console.log("series---", series)
		drawChart(chart, id, attr, {series: series, xAxis: {}}, {});
	}
</script>

<style scoped lang="scss">

</style>
