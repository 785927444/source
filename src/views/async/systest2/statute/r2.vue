<template>
  <div class="layout-col">
    <div class="ww100 flex-sc pl5 pr15 i15 mb10">  
      <span class="mr5 i15">状态：</span>
      <div class="w50x4 m">
        <el-select v-model="state.active" placeholder="请选择" filterable>
            <el-option v-for="(v, i) in state.actives" :key="v.value" :label="v.name" :value="v.value" />
        </el-select>
      </div>
      <span class="mr5 ml15 i15">名称：</span>
      <div class="w50x4">
        <el-input v-model="state.airName" placeholder="请输入"></el-input>
      </div>
      <el-button class="rad4 ptb5 plr8 flex-cc cursor bgi1 i15 ml10" @click.stop="handleClick('controls')">批量控制</el-button>
    </div>
    <!-- 内容 -->
    <div class="layout-row overlay warp p8 ba1">
        <div class="p8 ww20" v-for="(v, i) in !isNull(publicStore._public.homeDevices)?
        state.active!='All'&&!state.airName?publicStore._public.homeDevices.filter(a =>a['Operation_State']==state.active) : 
        state.active=='All'&&state.airName?publicStore._public.homeDevices.filter(a => a.name.indexOf(state.airName)!=-1) :
        state.active!='All'&&state.airName?publicStore._public.homeDevices.filter(a =>a['Operation_State']==state.active&&a.name.indexOf(state.airName)!=-1) :
        state.active=='All'&&!state.airName?publicStore._public.homeDevices : [] : []" :key="i">
            <div class="ww100 flex-col ba13 rad6 i15 hidden">
              <div class="flex-col ww100 p15">
                <div class="flex1 flex-sc">
                  <div class="w32 h32">
                    <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" fill="none" version="1.1" width="100%" height="100%" viewBox="0 0 32 32"><defs><clipPath id="master_svg0_5_88925"><rect x="0" y="0" width="32" height="32" rx="0"/></clipPath><linearGradient x1="1" y1="1.050394892692566" x2="-0.1456914299434196" y2="-0.298793109059701" id="master_svg1_5_75424"><stop offset="26.73705816268921%" stop-color="#3076F9" stop-opacity="1"/><stop offset="100%" stop-color="#EEF4FF" stop-opacity="1"/></linearGradient></defs><g clip-path="url(#master_svg0_5_88925)"><g><path d="M28.5,7.21890408203125L3.5,7.21890408203125C2.675,7.21890408203125,2,7.85649408203125,2,8.63581408203125L2,26.58328408203125C2,27.36258408203125,2.675,28.00018408203125,3.5,28.00018408203125L28.5,28.00018408203125C29.325,28.00018408203125,30,27.36258408203125,30,26.58328408203125L30,8.63581408203125C30,7.85649408203125,29.325,7.21890408203125,28.5,7.21890408203125ZM11.98438,5.73712408203125L11.98438,3.71368408203125C11.98438,3.30118408203125,11.64688,2.96368408203125,11.23438,2.96368408203125L6.75,2.96368408203125C6.3375,2.96368408203125,6,3.30118408203125,6,3.71368408203125L6,5.73712408203125L11.98438,5.73712408203125ZM25.9552,5.77359408203125L25.9552,3.75015308203125C25.9552,3.3376530820312498,25.6177,3.00015308203125,25.2052,3.00015308203125L20.7208,3.00015308203125C20.3083,3.00015308203125,19.9708,3.3376530820312498,19.9708,3.75015308203125L19.9708,5.77359408203125L25.9552,5.77359408203125Z" fill="url(#master_svg1_5_75424)" fill-opacity="1"/></g><g><path d="M16.848989843749997,21.891570800781253C16.78668984375,21.99985080078125,16.77018984375,22.13091080078125,16.80348984375,22.252690800781252C16.83688984375,22.37447080078125,16.916989843750002,22.47582080078125,17.02438984375,22.53182080078125L17.96658984375,23.02377080078125C18.167189843750002,23.12847080078125,18.41008984375,23.05087080078125,18.52268984375,22.84607080078125C18.899089843749998,22.161410800781248,19.45188984375,21.76876080078125,20.03908984375,21.76876080078125C20.64968984375,21.76876080078125,20.97288984375,21.97913080078125,21.46568984375,22.450630800781248C22.34988984375,23.30867080078125,23.17528984375,23.67477080078125,24.22538984375,23.67477080078125C25.52838984375,23.67477080078125,26.66518984375,22.94647080078125,27.42628984375,21.624130800781252C27.48858984375,21.51588080078125,27.50508984375,21.38479080078125,27.47168984375,21.263010800781252C27.43828984375,21.14123080078125,27.35818984375,21.039880800781248,27.25078984375,20.98388080078125L26.30868984375,20.49197080078125C26.10798984375,20.387190800781248,25.86518984375,20.464790800781252,25.75258984375,20.66963080078125C25.37608984375,21.35429080078125,24.82338984375,21.74694080078125,24.23608984375,21.74694080078125C23.62538984375,21.74694080078125,23.30228984375,21.53654080078125,22.80938984375,21.06491080078125C21.92528984375,20.20697080078125,21.09988984375,19.84088080078125,20.04978984375,19.84088080078125C18.74678984375,19.84088080078125,17.61008984375,20.56919080078125,16.848989843749997,21.891570800781253ZM4.51708984375,14.756010800781251L14.39880984375,14.756010800781251L14.39880984375,12.57257080078125L4.51708984375,12.57257080078125L4.51708984375,14.756010800781251Z" fill="#FFC860" fill-opacity="1"/></g></g></svg>
                  </div>
                  <span class="f15 ml10 white">{{v.name}}</span>
                </div>
                <div class="flex1 flex-sc mt12">
                  <div class="flex3">当前状态</div>
                  <div :class="find(state.actives, ['value', v.Operation_State], 'color')?find(state.actives, ['value', v.Operation_State], 'color'):''">
                    {{find(state.actives, ['value', v.Operation_State], 'name')}}
                  </div>
                </div>
                <div class="flex1 flex-sc mt12">
                  <div class="flex3">当前有功（kW）</div>
                  <div class="flex1 tr">{{v.Output_Power?Math.floor((v.Output_Power*100))/100 : 0}}</div>
                </div>
                <div class="flex1 flex-sc mt12">
                  <div class="flex3">当日发电时长（h）</div>
                  <div class="flex1 tr">{{v.Output_Time?Math.floor((v.Output_Time*100))/100 : 0}}</div>
                </div>
                <div class="flex1 flex-sc mt12">
                  <div class="flex3">当日发电电量（kWh）</div>
                  <div class="flex1 tr">{{v.Daily_Output?Math.floor((v.Daily_Output*100))/100 : 0}}</div>
                </div>
                <div class="flex1 flex-sc mt12">
                  <div class="flex3">累计发电电量（kWh）</div>
                  <div class="flex1 tr">{{v.Total_Output?Math.floor((v.Total_Output*100))/100 : 0}}</div>
                </div>
              </div>
              <div class="flex1 flex-cc pt15 bgi22 ptb12">
                  <div class="flex1 flex-cc">
                      <div class="flex-sc btn-bg2 cursor ptb5 pl5 pr12" @click.stop="toPath('/detail', {id: v.id, stationNum: v.stationNum, type: v.type, code: 'Output_Power', code2: 'Daily_Output'})">
                          <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" fill="none" version="1.1" width="14.933328628540039" height="13.25653076171875" viewBox="0 0 14.933328628540039 13.25653076171875">
                              <g>
                                  <path d="M12.5216,9.71945L14.9333,6.18986L13.2277,6.18986C13.0005,2.736,10.1323,0,6.6208,0C2.96,0,0,2.96747,0,6.62933C0,10.288,2.96747,13.2565,6.62827,13.2565C8.26986,13.2565,9.76853,12.656,10.9269,11.6661L10.0405,10.5248C9.12853,11.3227,7.936,11.8123,6.62827,11.8123C3.76427,11.8123,1.4432,9.49012,1.4432,6.62933C1.4432,3.76427,3.76534,1.44319,6.62827,1.44319C9.34294,1.44319,11.5605,3.53278,11.7835,6.18986L10.1099,6.18986L12.5216,9.71945L12.5216,9.71945Z" fill="#EFF5FF" fill-opacity="1"/>
                              </g>
                          </svg>
                          <span class="pl5">详情</span>
                      </div>
                  </div>
                  <div class="flex1 flex-cc">
                      <div class="flex-sc btn-bg2 cursor ptb5 pl5 pr12" @click.stop="logRef.onVisable(v)">
                          <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" fill="none" version="1.1" width="16" height="16" viewBox="0 0 16 16">
                              <defs>
                                  <clipPath id="master_svg0_11_026110"><rect x="0" y="0" width="16" height="16" rx="0"/></clipPath>
                              </defs>
                              <g clip-path="url(#master_svg0_11_026110)">
                                  <g>
                                      <path d="M14.3807259765625,7.5525965625C15.2815259765625,5.3278765625,13.8267259765625,2.8542125625,11.4446259765625,2.5602178625C10.4842659765625,2.4404344625,9.5157559765625,2.7081715625,8.7532859765625,3.3042185625C6.8604259765625,4.7799965625,6.8827559765625,7.6496565625,8.7983359765625,9.0958065625C10.0247359765625,10.0216665625,11.5997259765625,10.041356562499999,12.8106259765625,9.3518665625L13.8873259765625,10.7298865625C14.1138259765625,11.0203065625,14.5330259765625,11.0719565625,14.8233259765625,10.8452165625C15.1135259765625,10.6184565625,15.1648259765625,10.199336562500001,14.9379259765625,9.9092165625L13.8288259765625,8.4898065625C14.0518259765625,8.2153565625,14.2392259765625,7.9019865625,14.3807259765625,7.5525965625ZM8.7076459765625,6.505496562499999C8.4951659765625,4.9974765625,9.7710059765625,3.6983565625,11.2826259765625,3.8835565625C11.8926259765625,3.9566465625,12.4480259765625,4.2706165625,12.8253259765625,4.7555465625C13.7635259765625,5.9551365625,13.3084259765625,7.7182065625,11.9068259765625,8.3138565625C10.5052559765625,8.9095165625,8.9201259765625,8.013516562500001,8.7076459765625,6.505496562499999ZM1.3332929765625,4.552246562500001L4.6666259765625,4.552246562500001Q5.3332959765625,4.552246562500001,5.3332959765625,5.2189165625000005Q5.3332959765625,5.8855765625,4.6666259765625,5.8855765625L1.3332929765625,5.8855765625Q0.6666259765625,5.8855765625,0.6666259765625,5.2189165625000005Q0.6666259765625,4.552246562500001,1.3332929765625,4.552246562500001ZM1.3332929765625,8.552246562499999L5.3332959765625,8.552246562499999Q5.9999559765625,8.552246562499999,5.9999559765625,9.218916562499999Q5.9999559765625,9.885576562499999,5.3332959765625,9.885576562499999L1.3332929765625,9.885576562499999Q0.6666259765625,9.885576562499999,0.6666259765625,9.218916562499999Q0.6666259765625,8.552246562499999,1.3332929765625,8.552246562499999ZM1.3332929765625,12.5522265625L13.9999259765625,12.5522265625Q14.6666259765625,12.5522265625,14.6666259765625,13.2189265625Q14.6666259765625,13.8856265625,13.9999259765625,13.8856265625L1.3332929765625,13.8856265625Q0.6666259765625,13.8856265625,0.6666259765625,13.2189265625Q0.6666259765625,12.5522265625,1.3332929765625,12.5522265625Z" fill-rule="evenodd" fill="#EFF5FF" fill-opacity="1"/>
                                  </g>
                              </g>
                          </svg>
                          <span class="pl5">记录</span>
                      </div>
                  </div>
                  <div class="flex1 flex-cc">
                      <div class="flex-sc btn-bg1 cursor ptb5 pl5 pr12"  @click.stop="publicStore._public.init = !publicStore._public.init; ctrlRef.onVisable(v)">
                          <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" fill="none" version="1.1" width="10.666666984558105" height="9.333333969116211" viewBox="0 0 10.666666984558105 9.333333969116211">
                              <g>
                                  <path d="M8,7.33333L8,6.66667L9.33333,6.66667L9.33333,7.33333L10.6667,7.33333L10.6667,8.66667L9.33333,8.66667L9.33333,9.33333L8,9.33333L8,8.66667L0,8.66667L0,7.33333L8,7.33333ZM4.66667,4L4.66667,3.33333L6,3.33333L6,4L10.6667,4L10.6667,5.33333L6,5.33333L6,6L4.66667,6L4.66667,5.33333L0,5.33333L0,4L4.66667,4ZM1.33333,0.666667L1.33333,0L2.66667,0L2.66667,0.666667L10.6667,0.666667L10.6667,2L2.66667,2L2.66667,2.66667L1.33333,2.66667L1.33333,2L0,2L0,0.666667L1.33333,0.666667Z" fill="#EFF5FF" fill-opacity="1" style="mix-blend-mode:passthrough"/>
                              </g>
                          </svg>
                          <span class="pl5">控制</span>
                      </div>
                  </div>
              </div>
            </div>
        </div>
    </div>
    <Ctrl :state="state" ref="ctrlRef" />
    <Ctrls :state="state" ref="ctrlsRef" />
    <CtrlLog :state="state" ref="logRef" />
  </div>
</template>

<script lang="ts" setup>
  const { proxy }: any = getCurrentInstance();
  const publicStore = proxy.publicStore();
  const configStore = proxy.configStore();
  const state = reactive({
    ...publicStore.$state,
    active:"All",
    actives:[
      {name:"全部", value:"All", color: ''},
      {name:"停止", value:"0", color: 'i6'},
      {name:"运行", value:"1", color: 'i11'},
      {name:"异常", value:"2", color: 'i8'}
    ]
  });
  let ctrlRef = $ref()
  let ctrlsRef = $ref()
  let logRef = $ref()

  const handleClick = async(remark) => {
    if(remark == 'controls'){
      let val = {stationNum: publicStore.active.parent_id==0?publicStore.active.id:publicStore.active.parent_id}
      ctrlsRef.onVisable(val)
    }
  }
</script>

<style scoped lang="scss">
.btn-bg1{background: linear-gradient(90deg,rgba(65, 165, 176, 1) 0%,rgba(49, 199, 209, 0) 100%);border-radius: 4px;}
.btn-bg2{background: linear-gradient(90deg,rgba(44, 115, 222, 1) 0%,rgba(162, 187, 255, 0) 100%);border-radius: 4px;}
</style>
