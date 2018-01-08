<template>
  <div class="ComA">
    <!-- cinput是select和input那个长条样式  -->
    <div class="cinput">
      <Select v-model='selectVal' style='width:80px;float:left;' @on-change='checkVal'>
        <Option v-for='item in valList' :value='item.type' :key='item.type'>
          {{item.label}}
        </Option>
      </Select>
      <!--  input样式  -->
      <input type="text" class="inpStyle" placeholder="输入设备编号或轨迹查询" @keyup.13="showRes()">
    </div>
    <transition name='fade'>
      <div class="myContainer" v-if='closeCollapse'>
        <!--  x点击隐藏折叠  -->
        <div class="cheader">
          <div class="closeColl" @click='closeCol'>
            <Icon type='close' size='14' color='#fff' style='line-height:34px;' />
          </div>
        </div>
        <!--  折叠面板  用v-for写   -->
        <Collapse v-model='value1' :accordion='true' @on-change='getAmount()'>
          <Panel v-for='(item,index) in listType' :key='item.id' :name='index+""'>
            <div style="float:left;">{{item.sbmc}}({{item.sbsl}})</div>
            <div class="bfxz"><a style="color:#3f51b5;">播放选中</a></div>
            <div slot='content' class="colList">
              <li class="liContent">
                <p class="p_xh">序号</p>
                <p class="p_xh">操作</p>
                <p class="p_sbmc">设备名称</p>
              </li>
              <!--  上面的li是写死的  -->
              <!--  下面的li用v-for循环写出  -->
              <li class="liStyle" v-for="(item,key,index) in testArray" :class="[key%2==1?activeClass:liStyle]" @click='addClass(key)'>
                <p class="p_li">{{key+1}}</p>
                <p class="p_li">
                  <!--  多选框与checkNames实现了双向绑定,返回数组,数组中是选中的  -->
                  <input type='checkbox' :id="key" :value="item.sbmc" v-model='checkNames' style="margin-top:8px;" />
                  <label for='key'></label>
                </p>
                <p class="p_ip">{{item.sbmc}}</p>
              </li>
            </div>
          </Panel>
        </Collapse>
      </div>
    </transition>

    <!--  下面是轨迹查询对应的页面,当时组件划分错了。 应该下面单独做个组件  -->
    <transition name='fade'>
      <div class="trace" v-if='trace'>
        <div class="cheader">
          <div class="closeColl" @click='closeTrace'>
            <Icon type='close' size='14' color='#fff' style='line-height:34px;' />
          </div>
        </div>
        <!--  轨迹类型,  -->
        <div class="traceType">
          <ul>
            <li class="traceStyle" v-for='(item,key) in traceList'>{{item.gjlx}}</li>
          </ul>
        </div>
        
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios';
import bus from '../assets/event.js'
export default {
  name: 'HelloWorld',
  data () {
    return {
      closeCollapse:false,
      trace:false,
      selectVal:'设备',
      value1:'',
      valList:[],
      listType:[],
      testArray:[],
      traceList:[],
      liStyle:true,
      activeClass:'classObj',
      checkNames:[]
    }
  },
  methods:{
    closeCol(){
        this.closeCollapse=false;
    },
    // 关闭轨迹查询详情
    closeTrace(){
      this.trace=false;
    },
    checkVal(){
      // 控制台打印用户选择的类型  设备或轨迹
      console.log(this.selectVal); 
      if (this.selectVal=='轨迹') {
        this.closeCollapse=false;
      }else if(this.selectVal=='设备'){
        this.trace=false;
      }
    },
    // input框里面用户点击enter键之后的事件
    showRes(){
      // 判断一下,如果用户选择设备进行查询,显示设备信息
      if(this.selectVal=='设备'){
        // this.trace=false;
        this.closeCollapse=true;
        axios.get('../../static/data.json')
          .then((res)=>{
          //console.log(res.data.content);
          this.listType=res.data.content;
          })
            .catch((err)=>{
              console.log(err);
            })
      }else if(this.selectVal=='轨迹'){
          // this.closeCollapse=false;
           this.trace=true;
           // 请求数据
           axios.get('../../static/trace.json')
            .then((res)=>{
                console.log(res.data.content);
                this.traceList=res.data.content;
            })
              .catch((err)=>{
                console.log(err);
              })
      }
      
    },
    //  切换面板时触发的事件,返回当前已展开的面板的key,格式为数组
    getAmount(){
      //console.log(this.value1[0]);
      if(this.value1[0]!=undefined){
        // 如果点击了任何一个展开,请求数据
        axios.get(`../../static/${this.value1[0]}.json`)
          .then((res)=>{
          //console.log(res.data.content);
          this.testArray=res.data.content;
        })
           .catch((err)=>{
            console.log(err)
          })
      }
     
    },
    // 点击每个具体的li,右边显示详情
    addClass(key){
      //console.log(key);
      bus.$emit('send-to-tab',this.testArray[key].sbmc);
    }
  },
  created(){
    // 初始化的时候请求数据,
    axios.get('../../static/val.json')
     .then((res)=>{
        //console.log(res.data.content);
        this.valList=res.data.content;
     })
      .catch((err)=>{
        console.log(err);
      })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 0;
}
a {
  color: #42b983;
}

/*    左侧那个组件  */
.ComA{
  width: 400px;
  height: 300px;
/*border: 1px solid gray;*/
  float: left; 
  margin-left: 12px;
}
/*  上面select和input框样式  */

.cinput{
  width: 400px;
  height: 40px;
  float: left;
  border: 1px solid #c9c7c3; 
}

/*   input框的样式   */
.inpStyle{
  float: left;
  width: 260px;
  height: 38px;
  margin-left: 4px;
  font-size: 14px;
  border:none;
  outline: none;
}

/*  下面的折叠面板  */ 
.myContainer{
  width: 400px;
  height: 230px;
  margin-top: 9px;
  float: left;
  /*border:1px solid #dbdbdb;*/
}

.cheader{
  width: 100%;
  height: 32px;
  background: #284e8d;
}

/*  点击x  下面的消失  */
.closeColl{
  width: 30px;
  height: 32px;
  /*border:1px solid red;*/
  cursor: pointer;
  float: right;
}

.bfxz{
  float: right;
  margin-right: 10px;
}

/* liContent */
.liContent{
  width: 380px;
  height: 30px;
  float: left;
  border-right: 1px solid #c9c7c3;
}

/*  序号, 操作, 设备名称 */
.colList{
  width: 400px;
  height: 280px;
  overflow: auto;
  border-right: 0px;
  border-bottom: 1px solid #c9c7c3;
}

/*    序号   */
.p_xh{
  width: 40px;
  height: 30px;
  float: left;
  line-height: 30px;
  border-bottom: 1px solid #c9c7c3;
  border-right: 1px solid #c9c7c3;
}

/*  设备名称  */
.p_sbmc{
  width: 299px;
  text-align: center;
  height: 30px;
  float: left;
  line-height: 30px;
  border-bottom: 1px solid #c9c7c3;
}

/*  点击具体某个li展开时候那个li的样式 */
.liStyle{
  width: 380px;
  height: 36px;
  float: left;
}
.liStyle:hover{
  background: #284e8d;
  color: #fff;
  cursor: pointer;
}

.classObj{
  background: #f5f5f5;
}

.p_li{
  width: 40px;
  height: 36px;
  line-height: 36px;
  float: left;
  border-right: 1px solid #c9c7c3;
  border-bottom: 1px solid #c9c7c3;
}

/*  ip  地址那些个样式 */
.p_ip{
  width: 300px;
  height: 36px;
  line-height: 36px;
  float: left;
  border-right: 1px solid #c9c7c3;
  border-bottom: 1px solid #c9c7c3;
}


/*  轨迹详情  */
.trace{
  width: 400px;
  height: 200px;
  margin-top: 9px;
  float: left;
  border: 1px solid #c9c7c3;
}

/*  人员轨迹,车辆轨迹  */
.traceType{
  width:100%;
  height: 160px;
  margin-top: 1px;
  overflow: auto;
  float: left;
  /*border: 1px solid #c9c7c3;*/
}

.traceStyle{
  width: 100%;
  height: 38px;
  line-height: 38px;
  text-align: left;
  text-indent: 6px;
  border-bottom: 1px solid #c9c7c3;
}

.traceStyle:hover{
  background: #284e8d;
  color: #fff;
  cursor: pointer;
}


/*  transition 过渡动画  */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
</style>
