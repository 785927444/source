<template>
  <div class="layout-col">
    <!-- 搜索 -->
    <div class="ww100 flex-bc plr15">
      <div class="flex-sc">
        <el-button class="rad4 ptb5 plr8 flex-cc cursor bgi22 i21 bo-i21-1" @click.stop="handleClick('add')" :icon="Plus">运行模拟评估</el-button>
      </div>
      <div class="flex-ec cursor flex1">
        <div class="ml15 mr10">状态</div>
        <div class="flex w50x2">
          <el-select v-model="state.status" placeholder="请选择" style="width: 100%" clearable filterable>
            <el-option label="全部" value="" />
            <el-option v-for="(v, i) in statuss" :key="v.value" :label="v.name" :value="v.value" />
          </el-select>
        </div>
        <div class="ml15 mr10">场景</div>
        <div class="flex w50x2">
          <el-select v-model="state.scene" placeholder="请选择" style="width: 100%" clearable filterable>
            <el-option label="全部" value="" />
            <el-option v-for="(v, i) in scenes" :key="v.value" :label="v.name" :value="v.value" />
          </el-select>
        </div>
        <div class="ml15 mr10">时间</div>
        <div class="flex w50x8">
          <el-date-picker v-model="state.datetime" format="YYYY-MM-DD HH:mm:ss" value-format="YYYY-MM-DD HH:mm:ss"
            type="datetimerange" range-separator="-" start-placeholder="开始时间" end-placeholder="结束时间" />
        </div>
        <el-button class="rad4 ptb5 plr8 flex-cc cursor bgi22 i21 ml10 bo-i21-1 ml15" @click.stop="state.page = 1; init()" :icon="Search">搜索</el-button>
      </div>
    </div>
    <!-- 内容 -->
    <div class="layout-col p15">
      <div class="layout-col bss">
        <div class="row bgi22">
          <div :style="{textAlign: v.align}" v-for="(v, i) in state.content" :key="i" v-show="v.show" :class="typeof(v.key) == 'object'?v.width +' '+ 'mlr5':v.width">
            <span v-if="v.name">{{v.name}}</span>
            <div v-else class="w16 h16 rad2 m-auto cursor flex-cc" :class="state.click?'bgi21 white':'bg-white'" @click.stop="handleClick('selectAll', item)">
              <i-ep-select v-if="state.click" class="f10" />
            </div>
          </div>
        </div>
        <div class="table">
          <div class="row" :class="index%2 == 1?'bgi23':'bgi18'" v-for="(item, index) in state.list" :key="index" @click.stop="handleClick('active', item)">
            <div v-show="v.show" :class="v.width" :style="{textAlign: v.align}" v-for="(v, i) in state.content" :key="i">
              <span v-if="typeof(v.key) == 'object'" class="i21 cursor mlr5" :class="kk=='add'?'none':kk=='deal'&&item['clear_type']!=0?'none':''" v-for="(vv, kk, ii) in v.key" :key="ii" @click.stop="handleClick(kk, item)">{{vv}}</span>
              <div v-else-if="v.key=='-'" class="w16 h16 rad2 m-auto cursor flex-cc" :class="item['click']?'bgi21 white':'bg-white'" @click.stop="handleClick('active', item)">
                <i-ep-select v-if="item['click']" class="f10" />
              </div>
              <span v-else-if="v.key=='*'">{{(state.page-1)*state.limit + index+1}}</span>
              <span v-else-if="v.type == 'time'">{{item[v.key] == 0?'/':parseTime(item[v.key])}}</span>
              <span v-else-if="v.type == 'select'" :class="find(v.list, [v.value, item[v.key]], 'color')?find(v.list, [v.value, item[v.key]], 'color'):''">
                {{find(v.list, [v.value, item[v.key]], v.label)}}
              </span>
              <span v-else-if="v.type == 'range'" :class="findRange(v.value[0], [v.value[1], item[v.key]], 'color')?findRange(v.value[0], [v.value[1], item[v.key]], 'color'):''">
                {{findRange(v.value[0], [v.value[1], item[v.key]], v.value[2])}}
              </span>
              <span v-else-if="v.type == 'switch'">
                <el-switch v-model="item[v.key]" :active-text="v.value[1].name" :inactive-text="v.value[0].name" :active-value="v.value[1].value" :inactive-value="v.value[0].value" inline-prompt @change="handleClick('switch', item)"></el-switch>
              </span>
              <span v-else-if="v.type == 'input'"><el-input v-model="item[v.value]" placeholder="请输入" style="width: 150px;" size="small" /></span>
              <span v-else>{{isNull(item[v.key]) && v.key!=''? '/' : item[v.key]}}</span>
            </div>
          </div>
          <div v-if="state.empty" class="flex-cc ww100 tc ptb13 bgi18">没有数据</div>
        </div>
        <Pagination class="plr20" v-show="state.total>0" :total="state.total" v-model:page.sync="state.page" v-model:limit.sync="state.limit" @pagination="init" />
        <!-- 添加弹窗 -->
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
  import { Refresh, Search, Filter, Connection, Edit, Operation, Plus } from '@element-plus/icons-vue'
  const { proxy }:any = getCurrentInstance()
  const publicStore = proxy.publicStore()
  const configStore = proxy.configStore()
  let scenes = $ref([
    {value: 1, name: '削峰',      color: 'i21'},
    {value: 2, name: '填谷',      color: 'i8'},
    {value: 3, name: '缓解重过载', color: 'ca'},
    {value: 4, name: '需求响应',   color: 'i21'},
    {value: 5, name: '直接控制',   color: 'i8'},
  ])
  let statuss = $ref([
    {value: 0, name: '未执行', color: 'i8'},
    {value: 1, name: '待执行', color: 'i1'},
    {value: 2, name: '执行中', color: 'i21'},
    {value: 3, name: '已结束', color: 'i15'},
  ])
  const state = reactive({
    ...publicStore.$state,
    content: [
      // { width: 'w50',   show: true, align: 'center', key: '-', name: '' },
      { width: 'w70',   show: true, align: 'center', key: '*', name: '序号' },
      { width: 'w50x4', show: true, align: 'left',  key: 'scene', name: '场景', type: 'select', list: scenes, value: 'value', label: 'name' },
      { width: 'w50x4', show: true, align: 'left',  key: 'peak', name: '目标峰值（kW）' },
      { width: 'w50x4', show: true, align: 'left',  key: 'overload', name: '允许超载量' },
      { width: 'w50x5', show: true, align: 'left',  key: 'starttime', name: '响应开始时间', type: 'time' },
      { width: 'w50x5', show: true, align: 'left',  key: 'endtime', name: '响应结束时间', type: 'time' },
      { width: 'w50x4', show: true, align: 'left',  key: 'status', name: '状态', type: 'select', list: statuss, value: 'value', label: 'name' },
      { width: 'flex1', show: true, align: 'right', key: {deal: '处理'},  name: '操作' },
    ],
    model: 't_systest'
  })
  let exposeRef = $ref()
 
  onMounted(async() => {
    await getInit()
    await init()
  })

  const getInit = async () => {
    let query = {model: "t_sensor", args: `stationNum='${configStore.distributId}' and class='0'`}
    let res = await publicStore.http({Api: query})
    state.sensorList = proxy.isNull(res)? [] : res
  }

  const init = async () => {
    let args = `stationnum='${configStore.distributId}'`
    if(state.scene) args += ` and scene='${state.scene}'`
    if(state.status) args += ` and status='${state.status}'`
    if(!proxy.isNull(state.datetime)){
      let start = Date.parse(state.datetime[0]) + ''
      let end = Date.parse(state.datetime[1]) + ''
      args += ` and starttime>='${start}' and endtime<='${end}'`
    }
    let q1 = {limit: state.limit, page: state.page}
    let q2 = {field: `COUNT(*)`}
    let qs = args? {model: state.model, args: args} : {model: state.model}
    let query1 = {}
    let query2 = {}
    Object.assign(query1, qs, q1)
    Object.assign(query2, qs, q2)
    state.parmas = {Api1: query1, Api2: query2}
    let res = await publicStore.http(state.parmas)
    state.empty = proxy.isNull(res.Api1)? true : false
    state.list = proxy.isNull(res.Api1)? [] : res.Api1
    state.total = proxy.isNull(res.Api2)? 0 : res.Api2[0]['COUNT(*)']
  }

  const handleClick = (remark, val) => {
    if(remark == 'add'){
      exposeRef.onVisable(val)
    }
    if(remark == 'view'){
      alarmViewRef.onVisable(val)
    }
  }

</script>
  
<style scoped lang="scss">

</style>