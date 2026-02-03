<template>
  <div class="layout-row">
		<div class="hh100 flex3 tc ba1 hidden flex-sc">
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
