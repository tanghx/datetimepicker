<template>
    <div class="timePicker">
        <div class="datebody">
            <div class="time-hour">
                <table class="time-hour">
                    <thead>
                        <tr>
                            <th colspan="4">小时</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item,index) in hourList" :key="index">
                            <td v-for="hourItem in item" 
                                :key="hourItem" 
                                :class="{'active': hourItem == hour, 'disabled': ((start||start == 0) && hourItem < start) || ((end||end==0) && hourItem > end)}" 
                                @click="hourClick(hourItem)"
                            >{{hourItem}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="time-minute">
                <p>分钟</p>
                <happy-scroll hide-horizontal :scroll-top.sync="scrolltop" class="time-minute-list">
                    <ul>
                        <li v-for="item in minuteList" 
                            :key="item" 
                            :class="{'active': item == minute, 'disabled': (disabledHour&&(sm||sm==0)&&item<sm) || (disabledHour&&(em||em==0)&&item>em)}" 
                            @click="minuteClick(item)" 
                            v-scroll="item == minute"
                        >{{item}}</li>
                    </ul>
                </happy-scroll>
            </div>
        </div>
    </div>
</template>

<script>
import { HappyScroll } from 'vue-happy-scroll'
import 'vue-happy-scroll/docs/happy-scroll.css'
export default {
    data(){
        return {
            hourList: [],
            minuteList: [],
            hour: '00',
            minute: '00',
            disabledHour: false,
            disabledDay: this.disabledDate,
            scrolltop: 0
        }
    },

    props: ['current', 'type', 'range', 'startTime', 'endTime', 'disabledDate'],

    components: { HappyScroll },

    directives:{
        scroll: {
            inserted: (el, bind, vnode) => {
                if(bind.value){
                    console.log(el.offsetTop)
                    vnode.context.scrolltop = el.offsetTop
                }
            }
        }
    },

    beforeMount(){
        if(this.type == 'time' && this.range){
            this.disabledDay = true
        }
    },

    mounted(){
        let hours = [...Array(24).keys()].map(item=>{
            if(item<10){
                item = '0'+item
            }
            return item
        })
        for(let i = 0; i < hours.length; i += 4){
            this.hourList.push(hours.slice(i,i+4))
        }
        this.minuteList = [...Array(60).keys()].map(item=>{
            if(item<10){
                item = '0'+item
            }
            return item
        })
        let current = this.current;
        if(current){
            this.hour = current.split(':')[0]
            this.minute = current.split(':')[1]
        }
        if(this.hour == this.start || this.hour == this.end){
            this.disabledHour = true
        }
    },

    computed: {
        start(){
            if(this.startTime && this.disabledDay){
                return this.type == 'both' ? this.startTime.split(' ')[1].split(':')[0] : this.startTime.split(':')[0]
            }
        },
        end(){
            if(this.endTime && this.disabledDay){
                return this.type == 'both' ? this.endTime.split(' ')[1].split(':')[0] : this.endTime.split(':')[0] 
            }
        },
        sm(){
            if(this.startTime && this.disabledDay){
                return this.type == 'both' ? this.startTime.split(' ')[1].split(':')[1] : this.startTime.split(':')[1] 
            }
        },
        em(){
            if(this.endTime && this.disabledDay){
                return this.type == 'both' ? this.endTime.split(' ')[1].split(':')[1] : this.endTime.split(':')[1] 
            }
        }
    },
    
    methods: {
        hourClick(item){
            let s = this.start,
                e = this.end
            if( ( ( s || s == 0 ) && item < s) || ( ( e || e == 0 ) && item > e) ){
                return
            }
            if(( ( s || s == 0 ) && item == s) || ( ( e || e == 0 ) && item == e )){
                this.disabledHour = true
            }else{
                this.disabledHour = false
            }
            this.hour = item
            this.$emit('timeClick',{time:`${this.hour}:${this.minute}`,close: false})
        },
        minuteClick(item){
            let s = this.sm,
                e = this.em,
                d = this.disabledHour
            if( d && (s || s == 0) && item < s || d && (e || e == 0) && item > e ){
                return
            }
            this.minute = item
            this.$emit('timeClick',{time:`${this.hour}:${this.minute}`,close: true})
        }
    }
}
</script>

<style scoped type="text/css" lang="scss">
.timePicker{
    .datebody{
        overflow: hidden;
        padding: 0 10px;
        &>div{
            float: left;
        }
        .time-hour{
            width: 137px;
            table{
                border-collapse: collapse;
                border: none;
                width: 100%;
                thead th{
                    min-width: 29px;
                    height: 28px;
                    text-align: center;
                    font-weight: normal;
                    padding: 0px;
                }
                td{
                    height: 27px;
                    text-align: center;
                    vertical-align: middle;
                    padding: 0px;
                    border: 1px solid #e2e5ea;
                    cursor: pointer;
                    background: #fff;
                    &:hover,&.active{
                        color: #fff;
                        background: #34c9db;
                    }
                    &.disabled{
                        color: #bebebe;
                        background: #fff;
                        cursor: not-allowed;
                    }
                }
            }
        }
        .time-minute{
            margin-left: 10px;
            width: 50px;
            p{
                text-align: center;
                height: 28px;
                line-height: 28px;
            }
            .time-minute-list{
                width: 100%;
                height: 168px;
                border: 1px solid #e2e5ea;
                background: #ffffff;
            }
            ul{
                list-style: none;
                li{
                    text-align: center;
                    height: 28px;
                    line-height: 28px;
                    cursor: pointer;
                    &:hover,&.active{
                        color: #fff;
                        background: #34c9db;
                    }
                    &.disabled{
                        color: #bebebe;
                        background: #fff;
                        cursor: not-allowed;
                    }
                }
            }
        }
    }
    
}
</style>
