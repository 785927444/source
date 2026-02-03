<template>
  <div class="layout-col">
    <!-- 面包屑 -->
    <div class="ww100 flex-sc bs9 h40 mb15">
      <div class="cursor hh100 ptb10 plr20" v-for="(v, i) in state.type" :key="i" @click.stop="publicStore.rechart(); publicStore.actIndex = v.value"
      :class="publicStore.actIndex == v.value?'white ba2':'i1'">{{ v.name }}</div>
      <div class="flex1 hh100 flex-ec">
        <img src="@/assets/imgs/uu1.png" />
        <span class="ml15 cc cursor" @click.stop="toPath('/home')">首页</span>
        <span class="mlr10 cc">/</span>
        <span class="mr10 cursor">{{ find(state.type, ['value', publicStore.actIndex], 'name') }}</span>
        <img src="@/assets/imgs/uu2.png" />
      </div>
    </div>
    <!-- 页面 -->
		<div class="layout-col">
      <statute v-if="publicStore.actIndex == 'statute'" />
      <comm v-if="publicStore.actIndex == 'comm'" />
      <regulate v-if="publicStore.actIndex == 'regulate'" />
		</div>
  </div>
</template>

<script lang="ts" setup>
  import statute from "./statute";
  import comm from "./comm";
  import regulate from "./regulate";
	const { proxy }:any = getCurrentInstance()
	const configStore = proxy.configStore()
	const publicStore = proxy.publicStore()
	const state = reactive({
		...publicStore.$state,
    type: [
      {name: '规约一次性测试', value: 'statute'},
      {name: '通信性能测试', value: 'comm'},
      {name: '调控资源性能测试', value: 'regulate'},
    ]
	})

  onMounted(async () => {
    if(publicStore.actIndex == -1) {
      publicStore.actIndex = state.type[2]['value']
    }
  })
</script>

<style lang="scss">
 
</style>
