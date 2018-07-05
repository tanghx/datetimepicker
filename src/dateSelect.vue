<template>
    <div class="date-select">
        <div class="select-title" :data-id="selectedId" @click="open">
            {{selectedVal}}
            <em></em>
        </div>
        <div class="select-wrapper" v-clickoutside="wrapperHide" v-if="show">
            <happy-scroll hide-horizontal :scroll-top.sync="scrolltop">
                <ul>
                    <li :itemId="item.id" 
                        v-for="item in list" 
                        :key="item.id" 
                        :class="{'active':selectedId == item.id}" 
                        @click="select(item)" 
                        v-scroll="selectedId == item.id"
                    >{{item.value}}</li>
                </ul>
            </happy-scroll>
        </div>
    </div>
</template>

<script>
import { HappyScroll } from 'vue-happy-scroll'
import 'vue-happy-scroll/docs/happy-scroll.css'

export default {
    data(){
        return{
            selectedId: '',
            selectedVal: '',
            show: false,
            scrolltop: 0
        }
    },
    props:{
        list:{
            type: Array,
            default: []
        },
        row:{
            type: Number,
            default: 1,
        },
        selected:{
            type: Number,
            default: null,
        },
        disabled: {
            type: Boolean,
            default: false
        }
    },

    components: { HappyScroll },

    directives:{
        scroll: {
            inserted: (el, bind, vnode) => {
                if(bind.value){
                    vnode.context.scrolltop = 0
                    setTimeout(() => {
                        vnode.context.scrolltop = el.offsetTop
                    }, 10)
                }
            }
        },
        clickoutside: {
            bind (el, binding, vnode) {
                function documentHandler (e) {
                    if (el.contains(e.target)) {
                        return false;
                    }
                    if (binding.expression) {
                        binding.value(e);
                    }
                }
                el.__vueClickOutside__ = documentHandler;
                document.addEventListener('click', documentHandler);
            },
            unbind (el, binding) {
                document.removeEventListener('click', el.__vueClickOutside__);
                delete el.__vueClickOutside__;
            }
        }
    },
    mounted(){
        if(this.selected){
            this.selectedId = this.selected;
            this.selectedVal = this.list.filter(item=>{
                return item.id == this.selected
            })[0].value
        }
    },
    watch: {
        selected(nv,ov){
            this.selectedId = nv;
            this.selectedVal = this.list.filter(item=>{
                return item.id == nv
            })[0].value
        }
    },
    methods:{
        open(){
            if(this.disabled){
                return false
            }
            this.show = true
            return false
        },
        select(item){
            this.selectedId = item.id
            this.selectedVal = item.value
            this.show = false
            this.$emit('select',item.id)
        },
        wrapperHide(){
            this.show = false
        }
    }
}
</script>

<style scoped type="text/css" lang="scss">
.date-select{
    width: 100%;
    height: 100%;
    position: relative;
    .select-title{
        width: 100%;
        height: 100%;
        padding-right: 20px;
        position: relative;
        border: 2px solid #c4c9d3;
        box-sizing: border-box;
        text-align: center;
        line-height: 21px;
        cursor: pointer;
        em{
            position: absolute;
            display: block;
            width: 0;
            height: 0;
            border: 5px solid transparent;
            border-top-color: #575d69;
            right: 5px;
            top: 50%;
            margin-top: -2px;
        }
    }
    .select-wrapper{
        width: 100%;
        border: 2px solid #73d7e0;
        box-sizing: border-box;
        height: 154px;
        background: #ffffff;
        li{
            list-style: none;
            text-align: center;
            height: 30px;
            line-height: 30px;
            cursor: pointer;
            &:hover,&.active{
                background: #34c9db;
                color: #ffffff;
            }
        }
    }
}
</style>
