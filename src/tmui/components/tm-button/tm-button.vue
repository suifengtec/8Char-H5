<template>
	<tm-sheet 
	no-level
	:_style="{opacity:isclickOn||_disabled?0.7:1}" 
	hover-class="none"
	:round="btnSizeObj.round" 
	:width="btnSizeObj.w" 
	:height="btnSizeObj.h" 
	:padding="props.padding" 
	:margin="props.margin"
	:color="props.color" 
	:shadow="props.shadow"
	:transprent="props.transprent"
	:linear="props.linear"
	:linear-deep="props.linearDeep"
	:text="props.text"
	:outlined="props.outlined"
	:dark="props.dark"
	:follow-dark="props.followDark"
	:follow-theme="props.followTheme"
	:border-direction="props.borderDirection"
	:border-style="props.borderStyle"
	:border="props.border"
	:blur="props.blur"
	_class="flex flex-row flex-center ">
		<button 
		@click="onclick"
		@touchstart="touchstart" 
		@touchend="touchend" 
		@longpress="emits('longpress',$event)"
		@touchcancel="isclickOn=false;emits('touchcancel',$event)"
		@touchmove="emits('touchmove',$event)"
		@getphonenumber="emits('getphonenumber',$event)"
		@error="emits('error',$event)"
		@opensetting="emits('opensetting',$event)"
		@launchapp="emits('launchapp',$event)"
		@contact="emits('contact',$event)"
		@chooseavatar="emits('chooseavatar',$event)"
		:form-type="props.formType"
		:openType="props.openType"
		:appParameter="props.appParameter"
		:sessionFrom="props.sessionFrom"
		:sendMessageTitle="props.sendMessageTitle"
		:sendMessagePath="props.sendMessagePath"
		:sendMessageImg="props.sendMessageImg"
		:sendMessageCard="props.sendMessageCard"
		:loading="_load"
		:disabled="_disabled"
		:group-id="props.groupId"
		:guild-id="props.guildId"
		:public-id="props.publicId"
		:hover-start-time="10000000" 
		hover-stop-propagation hover-class="bhover" 
		class="button flex-1  flex-center" 
		:class="[customClass]"
		:style="[customCSSStyle,{height:(btnSizeObj.h-props.border)+'rpx',top:'-'+(props.border)+'rpx'}]">
			<slot>
				<tm-icon v-if="_icon" :userInteractionEnabled="false"  
				:color="_fontColor"  :_class="_label?'pr-10':''" :unit="props.unit" :fontSize="btnSizeObj.fontSize*0.9" :name="_icon"></tm-icon>
				<tm-text :userInteractionEnabled="false" :color="_fontColor" :fontSize="btnSizeObj.fontSize" :unit="props.unit"  :label="_label"></tm-text>
			</slot>
		</button>
	</tm-sheet>
</template>

<script lang="ts" setup>
/**
 * 按钮
 * @description 属性与原生按钮一致，对于微信登录授权进行了便捷封装。
 * @slot default 默认插槽。
 */
import {btnSize} from "./interface"
import {computed,PropType,ref,getCurrentInstance, PropsType } from "vue"
import tmSheet from "../tm-sheet/tm-sheet.vue"
import tmText from "../tm-text/tm-text.vue"
import tmIcon from "../tm-icon/tm-icon.vue";
import {custom_props,computedClass,computedStyle} from "../../tool/lib/minxs";
/**
 * 事件说明
 * @description 事件属性与原生 一 致
 * @links 见官网：https://uniapp.dcloud.io/component/button.html
 */
const emits = defineEmits<{
  (e: 'click', event: Event|TouchEvent): void
  (e: 'touchstart', event: Event|TouchEvent): void
  (e: 'touchmove', event: Event|TouchEvent): void
  (e: 'touchcancel', event: Event|TouchEvent): void
  (e: 'touchend', event: Event|TouchEvent): void
  (e: 'tap', event: Event|TouchEvent): void
  (e: 'longpress', event: Event|TouchEvent): void
  (e: 'getphonenumber', event: any): void
  (e: 'getUserInfo', event: any): void
  (e: 'getUserProfile', event: any): void
  (e: 'error', event: any): void
  (e: 'opensetting', event: any): void
  (e: 'launchapp', event: any): void
  (e: 'contact', event: any): void
  (e: 'chooseavatar', event: any): void
}>()

const proxy = getCurrentInstance()?.proxy??null;
const props = defineProps({
	...custom_props,
	transprent:{
		type:Boolean,
		default:false
	},
	followTheme:{
		type:Boolean,
		default:true
	},
	/**
	 * mini,small,normal,middle,large
	 */
	size:{
		type:String as PropType<btnSize>,
		default:'normal'
	},
	fontSize:{
		type:Number,
		default:0
	},
	fontColor:{
		type:String,
		default:""
	},
	margin:{
		type:Array as PropType<Array<number>>,
		default:()=>[0,16]
	},
	padding:{
		type:Array as PropType<Array<number>>,
		default:()=>[0,0]
	},
	
	shadow:{
		type:Number,
		default:2
	},
	width:{
		type:Number,
		default:0
	},
	height:{
		type:Number,
		default:0
	},
	//如果为true，采用块状flex布局，自动宽和高度。
	block:{
		type:Boolean,
		default:false
	},
	round:{
		type:Number,
		default:0
	},
	loading:{
		type:Boolean,
		default:false
	},
	disabled:{
		type:Boolean,
		default:false
	},
	//提供时，点击后会中转到对应页面。
	url:{
		type:String,
		default:''
	},
	label:{
		type:String,
		default:''
	},
	icon:{
		type:String,
		default:''
	},
	/**
	 * submit,reset 在tm-form中使用。
	 */
	formType:{
		type:String as PropsType<'submit'|'reset'|'filterCancel'|'filterConfirm'>,
		default:''
	},
	//开放能力
	openType:{
		type:String,
		default:''
	},
	//打开 APP 时，向 APP 传递的参数，open-type=launchApp时有效
	appParameter:{
		type:String,
		default:''
	},
	sessionFrom:{
		type:String,
		default:''
	},
	sendMessageTitle:{
		type:String,
		default:''
	},
	sendMessagePath:{
		type:String,
		default:''
	},
	sendMessageImg:{
		type:String,
		default:''
	},
	sendMessageCard:{
		type:String,
		default:''
	},
	groupId:{
		type:String,
		default:''
	},
	guildId:{
		type:String,
		default:''
	},
	publicId:{
		type:String,
		default:''
	},
  unit:{
    type:String,
    default:'rpx'
  }
})
/** -----------form专有------------ */
const formtype = computed(()=>props.formType)
//父级方法。
let FormParent:any = null;
let FilterParent:any = null;
if(formtype.value=='reset'||formtype.value=='submit'){
	FormParent = proxy?.$parent
	while (FormParent) {
		if (FormParent?.tmFormComnameId == 'tmFormId' || !FormParent) {
			break;
		} else {
			FormParent = FormParent?.$parent ?? undefined
		}
	}
}
//过滤器菜单 专用属性.
if(formtype.value=='filterCancel'||formtype.value=='filterConfirm'){
	FilterParent = proxy?.$parent
	while (FilterParent) {
		if (FilterParent?.FilterMenu == 'FilterMenu' || !FilterParent) {
			break;
		} else {
			FilterParent = FilterParent?.$parent ?? undefined
		}
	}
}
/** -----------end------------ */
//自定义样式：
const customCSSStyle = computed(()=>
{
	return {
		height:btnSizeObj.value.h+ props.unit,...computedStyle(props),
		border: '0px solid rgba(0, 0, 0, 0)',
		background: 'rgba(0, 0, 0, 0)',
		borderRadius: '0px'
	}
});
//自定类
const customClass = computed(()=>computedClass(props));
const isclickOn = ref(false);
const _load = computed(()=>props.loading)
const _disabled = computed(()=>props.disabled)
const _label = computed(()=>props.label)
const _icon = computed(()=>props.icon)
const sizeObj = computed(()=>{
  if(props.unit == 'px'){
    return {
      block:{w:0,h:80,fontSize:28,round:3},
      mini:{w:44,h:18,fontSize:10,round:2},
      small:{w:60,h:28,fontSize:11,round:3},
      normal:{w:110,h:40,fontSize:14,round:3},
      middle:{w:180,h:40,fontSize:15,round:3},
      large:{w:268,h:44,fontSize:16,round:4},
    }
  }
  return {
    block:{w:0,h:80,fontSize:28,round:3},
    mini:{w:88,h:36,fontSize:20,round:2},
    small:{w:120,h:56,fontSize:22,round:3},
    normal:{w:220,h:80,fontSize:28,round:3},
    middle:{w:360,h:80,fontSize:30,round:3},
    large:{w:535,h:88,fontSize:32,round:4},
  }
})
const btnSizeObj = computed(()=>{
	let fontSize = props.fontSize||0;
	
	if(props.block){
		return {w:0,h:props.height||sizeObj.value.block.h,fontSize:fontSize||sizeObj.value.block.fontSize,round:props.round==-1?0:(props.round||sizeObj.value.normal.round)}
	}
	return {
		w:props.width||sizeObj.value[props.size].w ,
		h:props.height||sizeObj.value[props.size].h,
		fontSize:fontSize||sizeObj.value[props.size].fontSize,
		round:props.round==-1?0:(props.round||sizeObj.value[props.size].round),
	}
})
const _fontColor = computed(()=>props.fontColor)


function touchstart(e:Event){
	isclickOn.value = true
	emits("touchstart",e)
}
function touchend(e:Event){
	isclickOn.value = false
	emits("touchend",e)
}

function onclick(e:Event){
		if(FormParent!=null && typeof FormParent !='undefined'&&formtype.value&&!props.loading){
			FormParent[formtype.value]();
		}
		if(FilterParent!=null && typeof FilterParent !='undefined'&&formtype.value&&!props.loading){
			FilterParent[formtype.value]();
		}
		emits('click', e);
		if (props.url !== '' && typeof props.url === 'string') {
			let url = props.url;
			if (url[0] !== '/') url = '/' + url;
			uni.navigateTo({
				url: url
			});
			return;
		}
		if(props.openType=='getUserInfo' || props.openType == 'getUserProfile'){
			// #ifdef MP-WEIXIN
			uni.getUserProfile({
				desc: '用于完善会员资料',
				success: function (userProfile) {
					if(userProfile.errMsg !='getUserProfile:ok'){
						uni.showToast({
							title:userProfile.errMsg,icon:'error',mask:true
						})
						return;
					}
					emits('getUserInfo', userProfile);
					emits('getUserProfile', userProfile);
				},
				fail: (error) => {
					console.log(error)
					uni.showToast({
						icon:"none",
						title:typeof error=="object"?error.errMsg:error
					})
				}
			});
			// #endif
		}
		
}





</script>

<style scoped>
.button{
	background: rgba(0, 0, 0, 0);
	border: 0px solid rgba(0, 0, 0, 0);
	padding: 0px;
	border-radius: 0px;
}

.buttonHover{
	background: rgba(0, 0, 0, 0);
}

.bhover{
	opacity: 0.7;
}
/* #ifndef APP-NVUE */
.button::after{
	background: transparent !important;
	background-color: transparent  !important;
	border:none  !important;
	border-radius: 0px  !important;
}

/* #endif */
</style>
