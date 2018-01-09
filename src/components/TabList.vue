<template>
	<div>
		<transition name='fade'>
			<div class="tab" v-if='show'>
				<div class="closeBar">
					<div class="close-icon" @click='closeTabList'>
						<Icon type='close' size='14' color='#fff' style='line-height:28px;' />
					</div>
				</div>
				<!--  上面是蓝色的长条,有一个关闭x按钮	-->
				<div class="content">
					{{detail}}
				</div>
				
			</div>
		</transition>
	</div>
</template>

<script type="text/javascript">
import axios from 'axios';
import bus from '../assets/event.js';
	export default{
		data(){
			return{
				show:false,
				detail:''
			}
		},
		// methods
		methods:{
			//  bus兄弟组件传递数据
		      receive(){
		          bus.$on('send-to-tab',(data)=>{
		            //console.log(data.substr(-3));
		            console.log(data);
		            this.detail=data;
		            this.show=true
		          });

		          // 把设备切换为轨迹时,隐藏TabList
		          bus.$on('close-TabList',()=>{
		          	//alert('切换');
		          	this.show=false;
		          })
		      },
		      // x点击关闭 TabList
		      closeTabList(){
		      	this.show=false;
		      }
		},
		// mounted
		mounted(){
			this.receive();
		}
	}
</script>

<style scoped>
.tab{
	width: 500px;
	height: 320px;
	/*border: 1px solid blue;*/
	float: right;
	margin-top: 0px;
	margin-right: 20px;
}

/*  上面的关闭按钮,点击后关闭TabList  */
.closeBar{
	width: 100%;
	height: 28px;
	background: #284e8d;
	border-top-left-radius: 4px;
	border-top-right-radius: 4px;
}

/*	 x那个容器,30*28大小	*/
.close-icon{
	width: 30px;
	height: 28px;
	float: right;
	cursor: pointer;
}

/*	 content 向用户展示的内容	*/
.content{
	width: 100%;
	height: 300px;
	border: 1px solid #c9c7c3;
}

/*  transition 过渡动画  */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
</style>
