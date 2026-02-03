<template>
  <div class="layout-col">
    <!-- 标题 -->
		<div class="h40 ww100 flex-sc relative mb5">
			<img class="hh100" src="@/assets/imgs/title.png" />
			<div class="ww100 hh100 flex-sc absolute pl35">
				<span class="fw flex1 ptb5">储能电量情况</span>
				<div class="flex-ec flex1"></div>
			</div>
		</div>
		<div class="hh100 flex3 tc ba1 hidden flex-sc">
			<el-carousel :interval="5000" arrow="always" class="hh100 ww100 tc" v-if="!isNull(publicStore._public.homeDevices)">
				<el-carousel-item v-for="index in Math.ceil(publicStore._public.homeDevices.length / 2)" :key="index">
					<div class="layout-row p10">
						<!-- 单数 -->
						<div class="hh100 ww50 p5 flex-col-cc" v-if="publicStore._public.homeDevices[(index - 1) * 2]">
							<div class="mb5 i12 f16 fw">
								{{publicStore._public.homeDevices[(index - 1) * 2]['Electricity']&&publicStore._public.homeDevices[(index - 1) * 2]['Total_Capacity']?
								Math.floor(publicStore._public.homeDevices[(index - 1) * 2]['Electricity']/publicStore._public.homeDevices[(index - 1) * 2]['Total_Capacity']*10000)/100:'0'}} %
							</div>
							<div class="relative hidden w50" style="border-radius: 0px 0px 10px 10px;">
								<img class="ww100 hh100" src="@/assets/imgs/en2.png" />
								<div class="absolute ww100 l0 b0 flex-ce" 
								:class="publicStore._public.homeDevices[(index - 1) * 2]['Electricity']&&publicStore._public.homeDevices[(index - 1) * 2]['Total_Capacity']?
								'hh' + (Math.floor(publicStore._public.homeDevices[(index - 1) * 2]['Electricity']/publicStore._public.homeDevices[(index - 1) * 2]['Total_Capacity']*100)):'hh0'">
									<img class="ww100 hh100" src="@/assets/imgs/en3.png" />
								</div>
							</div>
							<img class="w84" src="@/assets/imgs/en1.png" style="margin-top: -10px;" />
							<div class="mt10 i15">{{publicStore._public.homeDevices[(index - 1) * 2]['name']?publicStore._public.homeDevices[(index - 1) * 2]['name']:'未知'}}</div>
						</div>
						<!-- 双数 -->
						<div class="hh100 ww50 p5 flex-col-cc" v-if="publicStore._public.homeDevices[(index - 1) * 2 + 1]">
							<div class="mb5 i16 f16 fw">
								{{publicStore._public.homeDevices[(index - 1) * 2 + 1]['Electricity']&&publicStore._public.homeDevices[(index - 1) * 2 + 1]['Total_Capacity']?
								Math.floor(publicStore._public.homeDevices[(index - 1) * 2 + 1]['Electricity']/publicStore._public.homeDevices[(index - 1) * 2 + 1]['Total_Capacity']*10000)/100:'0'}} %
							</div>
							<div class="relative hidden w50" style="border-radius: 0px 0px 10px 10px;">
								<img class="ww100 hh100" src="@/assets/imgs/en5.png" />
								<div class="absolute ww100 hh50 l0 b0 flex-ce"
								:class="publicStore._public.homeDevices[(index - 1) * 2 + 1]['Electricity']&&publicStore._public.homeDevices[(index - 1) * 2 + 1]['Total_Capacity']?
								'hh' + (Math.floor(publicStore._public.homeDevices[(index - 1) * 2 + 1]['Electricity']/publicStore._public.homeDevices[(index - 1) * 2 + 1]['Total_Capacity']*100)):'hh0'">
									<img class="ww100 hh100" src="@/assets/imgs/en6.png" />
								</div>
							</div>
							<img class="w84" src="@/assets/imgs/en4.png" style="margin-top: -10px;" />
							<div class="mt10 i15">{{publicStore._public.homeDevices[(index - 1) * 2 + 1]['name']?publicStore._public.homeDevices[(index - 1) * 2 + 1]['name']: '未知'}}</div>
						</div>
					</div>
				</el-carousel-item>
			</el-carousel>
		</div>
  </div>
</template>

<script lang="ts" setup>
  const { proxy }:any = getCurrentInstance()
  const publicStore = proxy.publicStore()
	const configStore = proxy.configStore()
  const dictStore = proxy.dictStore()
  const state = reactive({
	  ...publicStore.$state,
  })
</script>
 
<style scoped lang="scss">
:deep(.el-carousel__container){
	height: 100%;
}
:deep(.el-carousel__arrow){
	background: rgba(28,128,245,0.2);
}
.rank_line{
  width: 4px;
  height: 100%;
  position: absolute;
  top: 24px;
  left: 24px;
  z-index: -1;
  opacity: 1;
  box-sizing: border-box;
  background: linear-gradient(to bottom, rgba(238, 246, 255, 1) 10%, rgba(238, 246, 255, 0) 100%);
}

</style>
