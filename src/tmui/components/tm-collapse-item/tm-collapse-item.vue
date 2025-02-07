<template>
	<view class="flex flex-col overflow" :class="[disabled ? 'opacity-7' : '']">
		<tm-sheet :transprent="props.transprent" @click="openAndClose"  
			:color="props.color" :text="disabled" :border="isActive?0:cborder" :linear="props.linear"
			:linearDeep="props.linearDeep"  :dark="props.dark"
			:followDark="props.followDark" :followTheme="props.followTheme" borderDirection="bottom" :margin="props.margin" :padding="props.padding"
			>
			<view :style="{height:props.height+'rpx'}" :userInteractionEnabled="false"  class="flex-row-center-start flex-row ">
				<view v-if="_tmCollapseIconPos=='left'" class="pr-16 flex-center">
					<tm-icon  :dark="props.dark"  :followDark="props.followDark"
						 :color="isActive ? props.activeColor : 'grey-1'"
						:name="isActive ?  _tmCollapseopenIcon : _tmCollapsecloseIcon" :font-size="20"
						></tm-icon>
				</view>
				<slot name="icon">
					<view v-if="_leftIcon" class="flex flex-center pr-16">
						<tm-icon style="line-height:0px" :color="_leftIconColor" :font-size="24" :name="_leftIcon"></tm-icon>
					</view>
				</slot>
				<view class="flex  flex-1" style="width:0px">
					<slot name="title" :data="{ isActive: isActive }">
						<tm-text _class=""  :dark="props.dark"  :followDark="props.followDark"
							:fontSize="props.titleSize" :color="isActive ? props.activeColor : ''"
							 :label="props.title"></tm-text>
					</slot>
				</view>
				<slot name="rightLabel"></slot>
				<view v-if="_tmCollapseIconPos=='right'" class="pl-16  flex-center" >
					<tm-icon  :dark="props.dark"  :followDark="props.followDark"
						 :color="isActive ? props.activeColor : 'grey-1'"
						:name="isActive ?  _tmCollapseopenIcon : _tmCollapsecloseIcon" :font-size="20"
						></tm-icon>
				</view>
			</view>
		</tm-sheet>
		<!-- v-if="isActive" -->
		<view v-if="_contentHeight"  class="flex overflow">
			<view class="flex content flex-col flex-1"  :class="[ isActiveAfter?'on':'']" :style="[
				_contentHeight&&isActiveAfter?{height:_contentHeight}:'',
				_contentHeight&&!isActiveAfter?{height:'0px',overflow:'hidden'}:'',
			]">
				<slot></slot>
			</view>
		</view>
		<view v-if="!_contentHeight&&isActive"   class="flex overflow">
			<view class="flex content flex-col flex-1"  :class="[ isActiveAfter?'on':'']">
				<slot></slot>
			</view>
		</view>
	</view>
</template>

<script lang="ts" setup>
/**
 * 折叠面板 tm-collapse
 * @description 有手风琴模式。请注意它里面只能旋转tm-collapse-item子组件，其它组件会失效。
 */
import {
	getCurrentInstance,
	computed,
	ref,
	inject,
	watchEffect,
	nextTick,
ComponentInternalInstance,
PropType,
} from 'vue';
import tmSheet from "../tm-sheet/tm-sheet.vue";
import tmText from "../tm-text/tm-text.vue";
import tmIcon from "../tm-icon/tm-icon.vue";
import tmTranslate from "../tm-translate/tm-translate.vue";
import {
	custom_props,
} from '../../tool/lib/minxs';
const proxy = getCurrentInstance()?.proxy??null;
const emits = defineEmits(['click'])
const props = defineProps({
	...custom_props,
	transprent:{
		type: Boolean,
		default: false
	},
	color: {
		type: String,
		default: 'white'
	},
	title: {
		type: String,
		default: ''
	},
	titleSize: {
		type: Number,
		default: 30
	},
	height: {
		type: [Number],
		default: 88
	},
	//如果指定了高度。可以全平台显示展开动画。否则在nvue端没有动画。
	contentHeight: {
		type: [Number,String],
		default: 0
	},
	//标识，用来展开和关闭的标识。
	name: {
		type: [Number, String],
		default: ''
	},
	activeColor: {
		type: [String],
		default: 'primary'
	},
	margin: {
		type: Array as PropType<Array<number>>,
		default: () => [0, 0],
	},
	padding: {
		type: Array as PropType<Array<number>>,
		default: () => [24, 0],
	},
	
	disabled: {
		type: [Boolean, String],
		default: false
	},
	leftIcon:{
		type: [String],
		default: ''
	},
	//默认为自动的activeColor颜色。
	leftIconColor:{
		type: [String],
		default: ''
	}
})
const _activekeyArray = inject("tmCollapseKeyList", computed(()=>[]))
const _tmCollapseIconPos = inject("tmCollapseIconPos", computed(()=>"left"))
const _tmCollapsecloseIcon = inject("tmCollapsecloseIcon", computed(()=>"tmicon-caret-right"))
const _tmCollapseopenIcon = inject("tmCollapseopenIcon", computed(()=>"tmicon-sort-down"))
const _leftIcon = computed(()=>props.leftIcon)
const _contentHeight = computed(()=>{
	if(!props.contentHeight) return 0;
	if(typeof props.contentHeight == 'string') return props.contentHeight;
	return uni.upx2px(props.contentHeight)+'px'
})
const isNvue = ref(false)
// #ifdef APP-NVUE
isNvue.value = true
// #endif
const isActiveAfter = ref(false)
//父级方法。
let parent:any = proxy?.$parent

while (parent) {
    if(parent?.tmCollapse=='tmCollapse'||!parent){
        break;
    }else{
        parent = parent?.$parent??undefined
    }
}
if(parent){
	//向父级缓存本子组件的key值。
	parent?.pushKey(props.name)
}
const cborder = ref(props.border ? props.border : parent?.border);
const isActive = computed(() => {
	let index = _activekeyArray.value.findIndex(el => {
		return el == props.name;
	})
	return index > -1;
})
const _leftIconColor = computed(()=>{
	if(props.leftIconColor) return props.leftIconColor;
	if(props.leftIconColor===""&&props.activeColor!==""&&isActive.value) return props.activeColor;
	return ""
})
watchEffect(()=>{
	if(isActive.value){
		setTimeout(function() {
			isActiveAfter.value = true;
		}, 20);
	}else{
		isActiveAfter.value = false
	}
})
function openAndClose(e:Event) {
	emits("click", e)
	if (props.disabled) return;
	parent?.setKey(props.name)
}
</script>

<style scoped>
/* #ifdef APP-NVUE */
.content{
	transition-duration: 0.3s;
	transition-timing-function:ease;
	transition-delay: 0ms;
	transition-property: height;
}

/* #endif */

/* #ifndef APP-NVUE */
.content{
	transition-duration: 0.3s;
	transition-timing-function:ease;
	transition-delay: 0ms;
	transition-property: max-height;
	max-height: 0px;
	overflow: hidden;
	box-sizing: border-box;
	will-change: max-height;
}
.content.on{
	max-height:800px;
}
.content.off{
	max-height:0px;
}
/* #endif */

</style>
