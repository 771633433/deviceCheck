<template>
	<div>
		<dialog-drag @close='shut' v-if='togg' :buttonPin='pin'>
			<transition name='fade'>
				<div class="tab" v-if='show'>
					<div class="closeBar">
						<div class="close-icon" @click='closeTabList'>
							<Icon type='close' size='14' color='#fff' style='line-height:28px;' />
						</div>
					</div>
					<!--  上面是蓝色的长条,有一个关闭x按钮	-->
					<div class="content">
						<!--  图片和几行文字介绍	-->
						<div class="box" v-for='(item,key,index) in src'>
							<div class="box_img">
								<img :class="imgClass[key]" width="100%" height="100%" :src="src[key]" @mouseover='toggEvent(key)' />
							</div>
							<!--  下面两行简短的文字	-->
							<div class="tip">
								<li class="li0">{{location[key]}}</li>
								<li class="li0">{{passTime[key]}}</li>
							</div>
						</div>
					</div>		
				</div>
			</transition>
		</dialog-drag>
	</div>
</template>

<script type="text/javascript">
import axios from 'axios';
import DialogDrag from 'vue-dialog-drag';
import bus from '../assets/event.js';
import Magnifier from 'magnifier';
	export default{
		components:{
			DialogDrag
		},
		data(){
			return{
				show:false,
				ipUrl:'',
				src:[],
				location:[],
				passTime:[],
				imgClass:[],
				togg:false,
				pin:false
			}
		},
		// methods
		methods:{
			//  bus兄弟组件传递数据
		      receive(){
		          bus.$on('send-to-tab',(data)=>{
		            //截取设备名称的后2位,拼接字符串,配合axios请求
		            //console.log(data.substr(-2));
		            this.show=true;
		            this.togg=true;
		            this.ipUrl=data.substr(-2);
		            // 请求数据第一页,页面内展示图片,支持放大镜效果
		            	axios.get(`../../static/${this.ipUrl}.json`)
		            	  .then((res)=>{
		            	  		//console.log(res.data.list);
		            	  		// for循环push进数组

		            	  		// 做个判断只push一次,再次点击的时候不push
		            	  		if(this.src.length==0){
									for(var i=0;i<res.data.list.length;i++){
			            	  			this.src.push(res.data.list[i].src);
			            	  			this.location.push(res.data.list[i].location);
			            	  			this.passTime.push(res.data.list[i].sj);
			            	  		}
		            	  		}	
		            	  })
		            	    .catch((err)=>{
		            	    	console.log(err);
		            	    });
		            	    console.log(this.src);
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
		      },
		      // 图片鼠标放上去有放大镜效果
		      toggEvent(key){
		      	new Magnifier('.img'+key);
		      	//console.log('长度是:'+this.src.length);
		      },
		      shut(){
     		 	//alert('关闭！');
     		 	this.togg=false
     		 }
		},
		// mounted
		mounted(){
			this.receive();
			// 循环产生数组,配合放大镜效果,给500个数据
			var imgs=[];
				for(var i=0;i<500;i++){
					imgs.push({['img'+i]:true});
				}

			var arr=[];
				for(var k=0;k<imgs.length;k++){
					this.imgClass.push(imgs[k]);
				}
		}
	}
</script>

<style scoped>
.tab{
	width: 540px;
	height: 340px;
	float: right;
	margin-top: 0px;
	margin-right: 0px;
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
	overflow: auto;
	border: 1px solid #c9c7c3;
}

/*	 box一个方块盒子,存放一张图片和下面几行文字介绍	*/
.box{
	width: 160px;
	height: 120px;
	/*border: 1px solid gray;*/
	float: left;
	margin-top: 24px;
	margin-left: 10px;
}

/*   放置图片	*/
.box_img{
	width: 100%;
	height: 90px;
	float: left;
}

/*	 两行简短的文字 	*/
.tip{
	width: 100%;
	height: 32px;
	float: left;
	border: 1px solid #c9c7c3;
}

.li0{
	width: 100%;
	height: 15px;
	text-align: left;
	/*border: 1px solid #c9c7c3;*/
	float: left;
	list-style: none;
}


/*  transition 过渡动画  */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
</style>


<style src="vue-dialog-drag/dist/vue-dialog-drag.css"></style>

<!-- optional dialog styles, see example -->
<style src="vue-dialog-drag/dist/dialog-styles.css"></style>



