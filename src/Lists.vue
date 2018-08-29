<template>
	<div>
		<div class="list-box">
			<drop class="drop list" @drop="handleDrop1(lists, ...arguments)">
				<drag v-for="(item, index) in lists[0]"
					class="drag"
					:key="item.name"
					:data-index="index"
					:class="{ [item.name]: true ,'new':item.isNew}"
					@dragstart="handleChildDragstart1"
					:transfer-data="{ item: item, index:index,list: lists[0], example: 'lists' ,tag:'list1'}">
						{{ item.name }}
				</drag>
			</drop>
		</div>

		<div class="list-box">
			<drop class="drop list" @drop="handleDrop2(lists, ...arguments)" @dragleave="handleLeave2(lists, ...arguments)" @dragover="handleOver2(lists, ...arguments)">
				<drag v-for="(item, index) in lists[1]"
					class="drag"
					:key="item.name"
					@dragstart="handleChildDragstart2"
					:class="{ [item.name]: true ,'new':item.isNew}"
					:data-index="index"
					:transfer-data="{ item: item,index:index, list: lists[1], example: 'lists',tag:'list2' }">
						{{ item.name }}
						<div>
							<img class="img" src="https://storybooks-vue.netlify.com/static/media/logo.82b9c7a5.png" alt="" srcset="">
							<!-- <h1>Test</h1> -->
						</div>
				</drag>
			</drop>
		</div>
	</div>
</template>

<script>

	import { Drag, Drop } from 'vue-drag-drop';
import Vue from 'vue';	
let variablePool={};
	export default {
		components: { Drag, Drop },
		data() {
			return {
				currentComponent1:null,
				currentComponent2:null,
				currentComponent2Index:null,
				lists: [
					[{name:'A'},{name: 'B'},{name: 'C'}],
					[{name:'D'},{name:  'E'},{name: 'F'}],
				],
			};
		},
		methods: {
			handleChildDragstart1(data, event) {
				console.log("handleChildDragstart1",data);
				this.currentComponent1=data.item;
				event.stopPropagation();
			},
			handleDrop1(toList, data) {
				if(!toList){
					return;
				}
				// toList=this.lists[0];
				// console.log("handleDrop1",toList,data);
				// let target=data.item;
				// const fromList = data.list;
				// if (fromList) {
				// 	toList.push(data.item);
				// 	fromList.splice(fromList.indexOf(data.item), 1);
				// 	toList.sort((a, b) => a > b);
				// }
			},
			dargOver1(){
				
			},
			handleChildDragstart2(data, event) {
				console.log("handleChildDragstart2",data);
				// this.currentComponent2=data.item;
				event.stopPropagation();
			},
			handleOver2(toList,data, event) {
				clearTimeout(variablePool.leaveTimeoutId);
				let that=this;
				event.stopPropagation();
				if(!toList){
					return;
				}
				toList=that.lists[1];
				//在本组内  交换位置
				if(data.tag=='list2'){
					return;
				}
				console.log("handleOver2",toList,data,event.target.dataset.index);
				let dataset=event.target.dataset;
				if(dataset.index!=null&&dataset.index!=undefined){//存在就设置
					that.currentComponent2Index=dataset.index;
				}
				if(that.currentComponent2Index!=null){
					if(!that.currentComponent2){
						let item={name:data.item.name,isNew:true};
						that.currentComponent2=item;
						toList.splice(that.currentComponent2Index,0,item);
					}else{
						toList.splice(toList.indexOf(that.currentComponent2),1);
						// delete that.currentComponent2.isNew;
						toList.splice(that.currentComponent2Index,0,that.currentComponent2);
					}
				}
				// that.currentComponent2=data.item;
			},
			handleLeave2(toList,data, event) {
				let that=this;
				console.error("handleLeave2",toList,data,event.target.dataset.index);
				event.stopPropagation();
				if(!toList){
					return;
				}
				toList=this.lists[1];
				//在本组内  交换位置
				if(data.tag=='list2'){
					return;
				}
				variablePool.leaveTimeoutId=setTimeout(function(){
					let dataset=event.target.dataset;
					if(that.currentComponent2Index!=null){
						if(that.currentComponent2){	
							toList.splice(toList.indexOf(that.currentComponent2),1);
						}
					}
					that.currentComponent2Index=null;
					that.currentComponent2=null
				},100);				
				// this.currentComponent2=data.item;
			},
			dargOver2(){
				
			},
			handleDrop2(toList, data,event) {				
				if(!toList){
					return;
				}
				toList=this.lists[1];
				let dataset =event.target.dataset;
				console.log("handleDrop2",toList,data);
				// const fromList = data.list;
				// if (fromList) {
					// toList.push(data.item);
					//在本组内  交换位置
					if(data.tag=='list2'){
						// let index=toList.indexOf(data.item);					
						if(data.index==dataset.index){//如果是自己则直接返回
							return;
						}
						toList=this.changeArrayElement(toList,data.index,dataset.index);
						// Vue.set
						Vue.set(this.lists, 1, toList)
					}else{// 不在本组内  新增在所在位置
						// toList.splice(dataset.index,0,data.item);
						// toList.push(data.item);	
						for(let i in toList){
							delete toList[i].isNew;
						}		
						Vue.set(this.lists, 1, toList)			
					}

					this.currentComponent2=null;

					// fromList.splice(fromList.indexOf(data.item), 1);
					// toList.sort((a, b) => a > b);
				// }
			},
			/**
			 * arr 数组 
			 * i  源索引
			 * j  目标索引
			 */
			changeArrayElement(arr,i,j){
				// let i=arr.indexOf(source),j=arr.indexOf(dest);
				let swap=arr[i];
				arr[i]=arr[j];
				arr[j]=swap;
				console.log(arr);
				return arr;
			}
		},
	};
</script>

<style scoped>
	.list-box{
		float: left;
	}
	.drag {
		/* display: inline-block;
		 */
		 display: block;
		 width: 200px;
	}
	.drag *{
		pointer-events: none;
	}
	.drag.A { background: #aaa; }
	.drag.B { background: #888; }
	.drag.C { background: #666; }
	.drag.D { background: #444; }
	.drag.E { background: #222; }
	.drag.F { background: #000; }
	.drag.new{
		border:3px dotted lightblue;
		background-color: #fff;
		/* height: 60px; */
	}
	.img{
		width: 30%;
	}
	.drop {
		display: inline-block;
		vertical-align: top;
		padding:5px 15px;
		margin-bottom: 20px;
		width: auto;
		height: auto;
	}
</style>
