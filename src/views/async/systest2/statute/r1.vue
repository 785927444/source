<template>
  <div class="layout-row">
    <div class="flex2 hh100 flex-col">
			<div class="layout-col relative p20 ba1">
				<div class="rank_line"></div>
				<div class="flex-col ww100">
					<div class="ww100 h30 flex-sc mb10" v-for="(v, i) in !isNull(publicStore._public.homeDevices)?publicStore._public.homeDevices.sort((a, b) => a.Total_Output - b.Total_Output).slice(0, 3):[]" :key="i" >
						<div class="w12 h12 rad100 bgi22 bo-white-3"></div>
						<div class="w80 tc" :class="i==0?'i6': i==1?'i21': i==2?'i30': i==3||i==4?'i29': 'white'">NO{{i+1}}</div>
						<div class="flex1 hh100 flex-sc relative">
							<img v-if="i==0" class="ww100 hh100 absolute" src="@/assets/imgs/title_rank1.png" />
							<img v-if="i==1" class="ww100 hh100 absolute" src="@/assets/imgs/title_rank2.png" />
							<img v-if="i==2" class="ww100 hh100 absolute" src="@/assets/imgs/title_rank3.png" />
							<img v-if="i==3 || i==4" class="ww100 hh100 absolute" src="@/assets/imgs/title_rank4.png" />
							<img v-else class="ww100 hh100 absolute" src="@/assets/imgs/title_rank5.png" />
							<div class="flex1 pl20">{{v.name}}</div>
							<div class="flex1 i15 flex-bc">
								<span>累计发电量</span>
								<span>
									<span class="white">{{v.Total_Output?Math.floor(100*v.Total_Output)/100:'/'}}</span>
									<span class="ml5">kWh</span>
								</span>
							</div>
						</div>
					</div>
				</div>
				<loopScroll :listData="!isNull(publicStore._public.homeDevices)?publicStore._public.homeDevices:[]" class="layout-col">      
					<div class="ww100 h30 flex-sc mb10" v-for="(v, i) in !isNull(publicStore._public.homeDevices)?publicStore._public.homeDevices.sort((a, b) => a.Total_Output - b.Total_Output).slice(3):[]" :key="i" >
						<div class="w12 h12 rad100 bgi22 bo-white-3"></div>
						<div class="w80 tc" :class="i==0||i==1?'i29': 'white'">NO{{i+4}}</div>
						<div class="flex1 hh100 flex-sc relative">
							<img v-if="i==0 || i==1" class="ww100 hh100 absolute" src="@/assets/imgs/title_rank4.png" />
							<img v-else class="ww100 hh100 absolute" src="@/assets/imgs/title_rank5.png" />
							<div class="flex1 pl20">{{v.name}}</div>
							<div class="flex1 i15 flex-bc">
								<span>累计发电量</span>
								<span>
									<span class="white">{{v.Total_Power?Math.floor(100*v.Total_Power)/100:'/'}}</span>
									<span class="ml5">kWh</span>
								</span>
							</div>
						</div>
					</div>
				</loopScroll>
			</div>
		</div>
		<div class="hh100 flex3 tc ba1 ml15 hidden flex-sc">
      <img class="hh100 ww100" v-if="!isNull(publicStore.active) && !isNull(publicStore.active.images) && publicStore.active.parent_id!='0'" :src="publicStore.active.images" />
			<el-carousel :interval="5000" arrow="always" class="hh100 ww100 tc" v-if="!isNull(publicStore.active) && !isNull(publicStore.active.images) && publicStore.active.parent_id=='0'">
				<el-carousel-item v-for="item in publicStore.active.images" :key="item">
					<img class="hh100 ww100" :src="item" />
				</el-carousel-item>
			</el-carousel>
		</div>
  </div>
</template>

<script lang="ts" setup>
  import { isNull } from '@/utils/common'
import loopScroll from 'loop-scroll'
  const { proxy }:any = getCurrentInstance()
  const publicStore = proxy.publicStore()
	const configStore = proxy.configStore()
  const dictStore = proxy.dictStore()
  const state = reactive({
	  ...publicStore.$state,
  })
  const defaultProps = {
    children: 'children',
    label: 'station_name',
  }
  let treeRef = $ref()
  let defaultId = $ref()

	const setTree = async(key, list) => {
    state.tree = proxy.treeData(list, state.key, state.parent, 'children')
    let node1 = proxy.findNode(state.tree, (node) => { return !node.children })
    let node2 = proxy.findNode(state.tree, (node) => { return node[state.key] == key })
    let node = proxy.isNull(node2)? node1 : node2
    await nextTick()
    defaultId = node[state.key]
    // treeRef.setCurrentKey(defaultId, true)
    publicStore.active = Object.assign({}, node)
  }

  const changeSelect = async(val) => {
		let node = proxy.findNode(state.tree, (node) => { return node[state.key] == val })
		if(node) publicStore.active = Object.assign({}, node)
  }

  // 暴露给父组件
  defineExpose({
    setTree,
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
