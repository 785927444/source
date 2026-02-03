<template>
  <div class="layout-row plr15 pb15">
    <!-- 左边 -->
    <div class="flex-col flex3 ww100 hidden">
      <l1 class="flex1 mb15" />
      <div class="flex-col flex2 ww100 hidden">
        <l2 class="flex1 mb15 " />
        <l3 class="flex1" />
      </div>
    </div>
    <!-- 中间 -->
    <div class="flex-col flex5 ww100 hidden mlr15">
      <div class="flex-col flex1 ww100 hidden mb15">
        <div class="h40 ww100 flex-sc relative mb5 i15">
          <div class="flex-sc flex1 hh100">
            <div class="mr10">区域</div>
            <div class="w50x5">
              <el-tree-select v-model="publicStore.active.id" placeholder="请选择" 
              @change="changeSelect"
              ref="treeRef" 
              class="flex1 c8" 
              :default-expand-all="true" 
              highlight-current 
              accordion 
              clearable 
              filterable 
              check-strictly 
              :render-after-expand="false"
              :data="state.tree" 
              :props="defaultProps" 
              node-key="id" />
            </div>
          </div>
        </div>
        <c1 class="flex1" />
      </div>
      <c2 class="flex2" />
    </div>
    <!-- 右边 -->
    <div class="flex-col flex3 ww100 hidden">
      <r1 class="flex1 mb15" />
      <div class="flex-col flex2 ww100 hidden">
        <r2 class="flex2" />
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
	import l1 from './l1'
	import l2 from './l2'
	import l3 from './l3'
	import c1 from './c1'
	import c2 from './c2'
  import r1 from './r1'
  import r2 from './r2'
  import scheduled from "@/utils/scheduled";
  const { createScheduled } = scheduled();
  const { proxy }: any = getCurrentInstance();
  const configStore = proxy.configStore();
  const publicStore = proxy.publicStore();
  const state = reactive({
    ...publicStore.$state,
    today: {}
  });
  const defaultProps = {
    children: 'children',
    label: 'station_name',
  }
  let treeRef = $ref()
  let defaultId = $ref()

  onMounted(async () => {
    await getInit();
    createScheduled("energy_control", 3 * 1000, () => {
      getDevicesData();
      getDevicesDailyput();
      getDevicesPower();
      getDevicesSoc();
    });
  });

	onBeforeUnmount(async() => {
		publicStore._public.DevicesDailyput = ''
		publicStore._public.DevicesPower = ''
		publicStore._public.DevicesSoc = ''
		publicStore.rechart()
	})

  watch(() => publicStore.active, async (val) => {
    if(proxy.isNull(val)) return
    getDevices();
  },{ immediate: false, deep: true });

  watch(() => publicStore._public.init, async (val) => {
    getDevices();
  },{ immediate: false, deep: true });

  const getInit = async (key) => {
    // 获取今日时间
    const today = new Date();
    const startOfToday = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 0, 0, 0, 0); // 精确到毫秒为 0
    const endOfToday = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 23, 59, 59, 999); // 精确到毫秒为 999
    state.today.start = startOfToday.getTime(); // 开始时间的时间戳
    state.today.end = endOfToday.getTime();     // 结束时间的时间戳
    let query1 = proxy.isNull(configStore.distributId) || configStore.distributId=='0'? { model: "t_sensor", args: `type='${publicStore.actIndex}'`} : { model: "t_sensor", args: `stationNum='${configStore.distributId}' and type='${publicStore.actIndex}'`}
    let query2 = { model: "t_sensor_control_property"}
    let query3 = { model: "t_sensor_template_control_point"}
    let res = await publicStore.http({ Api1: query1, Api2: query2, Api3: query3 })
    publicStore._public.sensorControls = proxy.isNull(res.Api2)? [] : res.Api2
    publicStore._public.templateControls = proxy.isNull(res.Api3)? [] : res.Api3.sort((a, b) => a.order - b.order)
    publicStore._public.allDevices = proxy.isNull(res.Api1)? [] : res.Api1
    let stationNums = [...new Set(publicStore._public.allDevices.filter(a => a.stationNum).map(a => a.stationNum))]
    state.stationNums = stationNums.map(a => `'${a}'`).join(',')
    state.bayNums = [...new Set(publicStore._public.allDevices.filter(a => a.bayNum).map(a => a.bayNum))]
    getStation(key)
  };

  const getStation = async (key) => {
    let query = {model: "t_station", args: `id IN (${state.stationNums}) OR parent_id IN (${state.stationNums})`}
    let res = await publicStore.http({ Api: query })
    let list = proxy.isNull(res)? [] : res
    state.list = list.filter(a => a.parent_id=='0' || state.bayNums.find(b=>b==a.id))
    let data = state.list.find(a=>a.parent_id == '0')
    data.images = state.list.filter(a=>a.images).map(a => {return a.images})
    setTree(data[state.key])
  }

	const setTree = async(key) => {
    state.tree = proxy.treeData(state.list, state.key, state.parent, 'children')
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

  const getDevices = async () => {
    if(proxy.isNull(publicStore.active)) return
    publicStore._public.devices = publicStore.active.parent_id == '0'? 
    publicStore._public.allDevices.filter(a=>a.stationNum == publicStore.active.id) :
    publicStore._public.allDevices.filter(a=>a.bayNum == publicStore.active.id)
    state.query = {model: "redis"}
    state.ids = []
    state.query.key = publicStore._public.devices.map((a) => {
      // ids
      let id = state.ids.find(b=>b == a.id)
      if(!id) state.ids.push(a.id)
      // 控制
      a.control = []
      let sensorControls = !proxy.isNull(publicStore._public.sensorControls)? publicStore._public.sensorControls.filter(b=>b.sensor_id == a.id) : []
      if(!proxy.isNull(sensorControls)){
        sensorControls.forEach(c => {
          if(!proxy.isNull(c.value)){
            try {
              let temp = JSON.parse(c.value)
              a.control.push(temp)
            } catch (error) {
              console.error("解析失败:", error.message)
            }
          }
        })
      } else{
        let templateControls = publicStore._public.templateControls.filter(b=>b.sensor_type == a.type)
        if(!proxy.isNull(templateControls)){
          templateControls.forEach(c => {
            if(!proxy.isNull(c.calcRuleExpres)){
              try {
                let temp = JSON.parse(c.calcRuleExpres)
                a.control.push(temp)
              } catch (error) {
                console.error("解析失败:", error.message)
              }
            }
          })
        }
      }
      // 实时
      return "mo:Hash:sensor:" + a.id;
    });
    getDevicesData();
    getDevicesDailyput();
    getDevicesPower();
    getDevicesSoc();
  };

  // 实时请求设置信息
  const getDevicesData = async() => {
    if (!proxy.isNull(publicStore._public.devices)){
      let params = {getApi: state.query}
      let res = await publicStore.http(params)
      let homeInfo = {}
      let homeDevices = []
      publicStore._public.devices.forEach(vv => {
        // 重置实时
        let device =  Object.assign({}, vv)
        let redis_exist = res.find(a=>a.sensor_id == vv.id)
        let redis_data = redis_exist? redis_exist : {}
        Object.assign(device, redis_data)
        if(device.isAlarm) device.alarm = device.isAlarm>0? '1' : '0'
        setData(homeInfo, redis_data)
        // 获取设备
        homeDevices.push(device)
      })
      publicStore._public.homeInfo = homeInfo
      publicStore._public.homeDevices = homeDevices
      // console.log("publicStore._public.homeInfo---", publicStore._public.homeInfo)
      // console.log("publicStore._public.homeDevices---", publicStore._public.homeDevices)
    } else{
      publicStore._public.homeInfo = {};
      publicStore._public.homeDevices = [];
    }
  }

  // 设置数据
  const setData = (info, data) => {
    info['sum'] = !info['sum'] ? 1 : info['sum'] + 1
    Object.keys(data).forEach((key)=>{
      if (key.indexOf('_tc')==-1 && (typeof data[key] === 'number' || (typeof data[key] === 'string' && data[key].trim() !== '' && !isNaN(Number(data[key].trim()))))) {
        if(key == 'timestamp'){
          // 最大
          if(!info[key] || info[key] && info[key]<data[key]) info[key] = data[key]
        }else{
          // 累计
          info[key] = !info[key] ? Number(data[key]) : Number(info[key]) + Number(data[key])
        }
        // 工况
        if(key == 'offline') {
          info[key+data[key]] = !info[key+data[key]] ? 1 : info[key+data[key]] + 1
        }
        // 告警
        if(key == 'isAlarm') {
          if(data[key] > 0) info['alarm1'] = !info['alarm1'] ? 1 : info['alarm1'] + 1
          if(data[key] == 0) info['alarm0'] = !info['alarm0'] ? 1 : info['alarm0'] + 1
        }
        // 运行状态
        if(key == 'Operation_State') {
          info[key+data[key]] = !info[key+data[key]] ? 1 : info[key+data[key]] + 1
        }
      } 
    })
  }

  //SOC
  const getDevicesSoc = async() => {
    if (!proxy.isNull(publicStore._public.devices)){
      let name = 'DevicesSoc'
      let code = "SOC"
      let num = 20
      let ids = [...new Set(publicStore._public.devices.filter(a => a.id).map(a => a.id))]
      let length = ids.length
      let limit = num * length > 100? 100 : num * length
      let query = { model: "t_sensor_measure_float", order: "timestamp desc", limit: limit, args: `measurement_code IN ('${code}') and sensor_id IN (${ids})`}
      let res = await publicStore.http({ Api: query })
      let list = proxy.isNull(res)? [] : res
      let chart = state.ids.map(v => {
        let sensor = publicStore._public.devices.find(a=>a.id == v)
        let id = ''
        let name = ''
        if(sensor) {
          name = sensor.name
          id = sensor.id
        }
        return {name: name, id: id, data: []}
      })
      list.forEach((v, i)=> {
        let value = Math.floor(100*Number(v.value))/100
        let time = proxy.parseTime(v.timestamp, '{m}-{d} {h}:{i}:{s}')
        let item = [time, value, v.timestamp]
        let chartTemp = chart.find(a=>a.id == v.sensor_id) 
        if(chartTemp){
          let exist = chartTemp.data.find(a=>a[0]==time)
          v[code] = value
          if(exist){
            exist[1] += value
          }else{
            chartTemp.data.unshift(item)
          }
        }
      })
      publicStore._public[name] = chart
    }else{
      publicStore._public[name] = [{name: '', data: []}];
    }
  }

  // 充放电量
  const getDevicesDailyput = async() => {
    let name = 'DevicesDailyput'
    let code1 = "Daily_Input"
    let code2 = "Daily_Output"
    let code = `'${code1}','${code2}'`
    let chart = [
      {name: '充电量', code: code1, data: []},
      {name: '放电量', code: code2, data: []}
    ]
    if (!proxy.isNull(publicStore._public.devices)){
      let query = getCodeQuery(code)
      let res = await publicStore.http({ Api: query })
      let list = proxy.isNull(res)? [] : res
      let timekey = !proxy.isNull(publicStore.active) && publicStore.active.parent_id == '0'?'date':'timestamp'
      let codekey = !proxy.isNull(publicStore.active) && publicStore.active.parent_id == '0'?'code':'measurement_code'
      list.forEach((v, i)=> {
        let value = Math.floor(100*Number(v.value))/100
        let timestamp = v[timekey]
        let time = proxy.parseTime(timestamp, '{m}-{d} {h}:{i}:{s}')
        let item = [time, value, timestamp]
        let chartTemp = chart.find(a=>a.code == v[codekey]) 
        if(chartTemp){
          let exist = chartTemp.data.find(a=>a[0]==time)
          v[code] = value
          if(exist){
            exist[1] += value
          }else{
            chartTemp.data.unshift(item)
          }
        }
      })
    } 
    publicStore._public[name] = chart
  }

  // 充放功率
  const getDevicesPower = async() => {
    let name = 'DevicesPower'
    let code1 = "Input_Power"
    let code2 = "Output_Power"
    let code = `'${code1}','${code2}'`
    let chart = [
      {name: '充电功率', code: code1, data: []},
      {name: '放电功率', code: code2, data: []}
    ]
    if (!proxy.isNull(publicStore._public.devices)){
      let query = getCodeQuery(code)
      let res = await publicStore.http({ Api: query })
      let list = proxy.isNull(res)? [] : res
      let timekey = !proxy.isNull(publicStore.active) && publicStore.active.parent_id == '0'?'date':'timestamp'
      let codekey = !proxy.isNull(publicStore.active) && publicStore.active.parent_id == '0'?'code':'measurement_code'
      list.forEach((v, i)=> {
        let value = Math.floor(100*Number(v.value))/100
        let timestamp = v[timekey]
        let time = proxy.parseTime(timestamp, '{m}-{d} {h}:{i}:{s}')
        let item = [time, value, timestamp]
        let chartTemp = chart.find(a=>a.code == v[codekey]) 
        if(chartTemp){
          let exist = chartTemp.data.find(a=>a[0]==time)
          v[code] = value
          if(exist){
            exist[1] += value
          }else{
            chartTemp.data.unshift(item)
          }
        }
      })
    } 
    publicStore._public[name] = chart
  }

  // 获取测点参数
  const getCodeQuery = (code) => {
    let query = {}
    if(!proxy.isNull(publicStore.active) && publicStore.active.parent_id == '0'){
      let limit = 20
      let args = `code IN (${code}) and station_num='${publicStore.active.id}' and type='${publicStore.actIndex}'`
      if(!proxy.isNull(state.datetime)) args += ` and date>='${state.datetime.start}' and date<='${state.datetime.end}'`
      query = { model: "t_sensor_measure_float_station", order: "date desc", limit: limit, args: args }
    } else {
      let num = 10
      let length = publicStore._public.devices.length
      let limit = num * length > 100? 100 : num * length
      let ids = state.ids.map(a => `'${a}'`).join(',')
      let args = `measurement_code IN (${code}+'') and sensor_id IN (${ids})`
      if(!proxy.isNull(state.datetime)) args += ` and timestamp>='${state.datetime.start}' and timestamp<='${state.datetime.end}'`
      query = { model: "t_sensor_measure_float", order: "timestamp desc", limit: limit, args: args }
    }
    return query
  }
</script>

<style lang="scss">
 
</style>
