<template>
    <transition
            name="dialog-fade"
    >
        <div v-show="visible" class="hm_dialog_wrapper" @click.self="handleWrapperClick">
            <div class="hm_dialog" :style="style">
                <div><slot></slot></div>
            </div>
        </div>
    </transition>
</template>
<script lang="ts">
    let dialogId = 1;
    import PopupManager from '../utils/popup-manager'
    import {Vue,Prop,Watch,Component} from 'vue-property-decorator'
    @Component({
        name:"Dialog"
    })
    export default class extends Vue{
        private dialogId:any;
        @Prop({default:true}) private visible!:boolean
        @Prop({default:""}) private width!:string
        @Prop({default:false}) private modal!:boolean
        @Prop({default:true}) private closeOnClickModal!:boolean
        @Prop({default:true}) private modalAppendToBody!:boolean
        @Prop({default:false}) private appendToBody!:boolean
        @Prop() private beforeClose!:Function

        @Watch('visible')
        visibleChange(val){
            if(val)
            {
                if(this.appendToBody)
                {
                    document.body.appendChild(this.$el);
                }
                this.open();
            }else{
                this.close();
            }
        }
        get style(){
            interface Style{
                width?:string,
            }
            let style : Style = {};
            if(this.width)
            {
                style.width = this.width;
            }
            return style;
        }
        beforeMount(){
            this.dialogId = `dialog-${dialogId++}`;
            PopupManager.register(this.dialogId,this);
        }
        mounted(){
            console.log("mounted")
            if(this.visible)
            {
                this.open();
                if(this.appendToBody)
                {
                    document.body.appendChild(this.$el);
                }
            }
        }
        beforeDestroy(){
            PopupManager.deregister(this.dialogId);
            PopupManager.closeModal(this.dialogId);
        }
        destroyed(){
            if(this.appendToBody && this.$el && this.$el.parentNode)
            {
                this.$el.parentNode.removeChild(this.$el);
            }
        }
        open(){
            this.doOpen();
        }
        doOpen(){
            let dom = this.$el as HTMLElement;
            if(this.modal)
            {
                PopupManager.openModal(this.dialogId,PopupManager.nextZIndex(),this.modalAppendToBody ? null : dom);
            }
            dom.style.zIndex = PopupManager.nextZIndex();
        }
        close(){
            this.doClose();
        }
        doClose(){
            this.afterClose();
        }
        afterClose(){
            PopupManager.closeModal(this.dialogId);
        }
        handleWrapperClick(){
            if(!this.closeOnClickModal) return;
            this.handleClose();
        }
        handleClose(){
            if(typeof this.beforeClose === 'function')
            {
                this.beforeClose(this.hide);
            }else{
                this.hide();
            }
        }
        hide(){
            this.$emit("update:visible",false);
        }
    }
</script>
<style lang="scss">
    .hm_dialog_wrapper{
        position: fixed;
        left:0;
        top:0;
        right:0;
        bottom:0;
        overflow: auto;
    }
    .hm_dialog{
        margin:50px auto;
        background-color: #fff;
        border-radius: 2px;
        box-sizing: border-box;
    }
    .hm_modal{
        position: fixed;
        left:0;
        top:0;
        width:100%;
        height:100%;
        background-color: rgba(3,3,3,0.5);
    }
    .dialog-fade-enter-active {
        animation: dialog-fade-in .3s;
    }

    .dialog-fade-leave-active {
        animation: dialog-fade-out .3s;
    }

    @keyframes dialog-fade-in {
        0% {
            transform: translate3d(0, -20px, 0);
            opacity: 0;
        }
        100% {
            transform: translate3d(0, 0, 0);
            opacity: 1;
        }
    }

    @keyframes dialog-fade-out {
        0% {
            transform: translate3d(0, 0, 0);
            opacity: 1;
        }
        100% {
            transform: translate3d(0, -20px, 0);
            opacity: 0;
        }
    }
    .hm-modal-enter {
        animation: hm-modal-in .2s ease;
    }

    .hm-modal-leave {
        animation: hm-modal-out .2s ease forwards;
    }

    @keyframes hm-modal-in {
        0% {
            opacity: 0;
        }
        100% {
        }
    }

    @keyframes hm-modal-out {
        0% {
        }
        100% {
            opacity: 0;
        }
    }
</style>