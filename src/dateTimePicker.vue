<template>
    <div class="picker" v-clickoutside="pickerHide">
        <label>
            <em :class="iconClass"></em>
            <input type="text" readonly :placeholder="placeholder" v-model="datetime" @focus="pickerShow = true">
        </label>
        <div class="dateTime-picker" v-if="pickerShow" :class="{'time-picker-only': type == 'time'}">
            <div class="date-head" v-if="type != 'time'">
                <div class="date-prev" @click="prev"></div>
                <div class="date-center">
                    <div class="date-year">
                        <date-select :list="yearList" :disabled="timeShow" :selected="currentYear" @select="selectYear"></date-select>
                    </div>
                    <div class="date-month">
                        <date-select :list="monthList" :disabled="timeShow" :selected="currentMonth" @select="selectMonth"></date-select>
                    </div>
                </div>
                <div class="date-next" @click="next"></div>
            </div> 
            <date-picker v-if="dateShow" :current="currentDate" :startTime="startTime" :endTime="endTime" :dayList="dayList" :type="type" @dateClick="dateClick" @timeClick="datetimeClick"></date-picker> 
            <time-picker v-if="timeShow" :current="currentTime" :startTime="startTime" :endTime="endTime" :disabledDate="disabledDate" :type="type" :range="range" @timeClick="timeClick"></time-picker>
        </div>
    </div>
</template>

<script>
/*
config
@param  string type 日期时间选择器类型 'date'只有日期 'time'只有时间 'both'日期时间  
@param  boolean range 是否选择范围
@param  boolean clearBtn 是否有清空按钮
@param  string placeholder 非范围选择时的占位符
@param  string startPlaceholder 范围选择时开始日期的占位符
@param  string endPlaceholder 范围选择时结束日期的占位符
*/
import dateSelect from './dateSelect'
import datePicker from './datePicker'
import timePicker from './timePicker'

export default {
    data () {
        return {
            pickerShow: false,

            dateShow: this.type == 'time'?false: true,
            timeShow: this.type == 'time'?true:false,

            datetime: '',
            yearList: [],
            monthList: [],
            
            currentYear: new Date().getFullYear(),
            currentMonth: new Date().getMonth() + 1,

            currentDate: null,
            currentTime: null,

            startTime: '',
            endTime: '',
            disabledDate: false
        }
    },
    components:{
        dateSelect,datePicker,timePicker
    },
    props:[ 'type','iconClass','start','end','range', 'placeholder'],
    directives:{
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
    beforeMount(){
        for(var i = 0; i <= 100; i++){
            this.yearList.push({
                id: 1950+i,
                value: 1950+i+'年'
            })
            if(i<12){
                this.monthList.push({
                    id: i+1,
                    value: i+1+'月'
                })
            }
        }
    },
    watch: {    
        pickerShow(val){
            if(!val){
                this.dateShow = this.type == 'time'?false: true
                this.timeShow = this.type == 'time'?true:false
            }else{
                if(this.datetime){
                    switch (this.type) {
                        case 'time':
                            this.currentTime = this.datetime
                            break;
                        case 'date':
                            this.currentDate = this.datetime
                            this.currentYear = Number(this.datetime.split('-')[0])
                            this.currentMonth = Number(this.datetime.split('-')[1])
                            break;
                        default:
                            let date = this.datetime.split(' ')[0]
                            let time = this.datetime.split(' ')[1]
                            this.currentDate = this.datetime
                            this.currentTime = time
                            this.currentYear = Number(date.split('-')[0])
                            this.currentMonth = Number(date.split('-')[1])
                            break;
                    }
                }
                this.initRange()
            }
        },
        datetime(val){
            this.$emit('timeInput',val)
        }
    },
    computed: {
        dayList(){
            let y = this.currentYear
            let m = this.currentMonth
            let d = new Date(y,m,1,0,0,0)
            let lastDay = new Date(d-1)
            let dayLength = lastDay.getDate()
            return this.createCalendar(dayLength,y,m)
        }
    },
    methods: {
        prev(){
            if(this.timeShow){
                return
            }
            this.currentMonth --
            if(this.currentMonth < 1){
                this.currentMonth = 12;
                this.currentYear -- 
            }
        },
        next(){
            if(this.timeShow){
                return
            }
            this.currentMonth ++
            if(this.currentMonth>12){
                this.currentMonth = 1;
                this.currentYear ++ 
            }
        },
        selectYear(id){
            this.currentYear = id
        },
        selectMonth(id){
            this.currentMonth = id
        },
        createCalendar(len,y,m){
            let list = [...Array(len).keys()].map(item=>{return item+=1})
            let firstDay = new Date(y,m-1,1).getDay()
            list = [...Array(firstDay),...list]
            let dayList = []
            for(let i = 0; i < 6; i++){
                let row = []
                for(let j = 0; j < 7; j++){
                    let index = i * 7 + j;
                    if(list[index]){
                        row.push({
                            value: list[index],
                            id: `${y}-${m}-${list[index]}`,
                            time: new Date(y,m-1,list[index]).getTime()
                        })
                    }else{
                        row.push({
                            value: null,
                            id: null
                        })
                    }
                }
                dayList.push(row)
            }
            return dayList
        },
        dateClick(data){
            let item = data.item
            let d = item.id.split('-')
            let date = `${d[0]}-${this.addZero(d[1])}-${this.addZero(d[2])}`
            if(this.type == 'date'){
                this.datetime = date
                this.pickerShow = false
            }else{
                this.dateShow = false
                this.timeShow = true
                this.disabledDate = data.disabled
                if(this.datetime == ''){
                    if(this.disabledDate && this.startTime){
                        this.datetime = `${date} ${this.startTime.split(' ')[1]}`
                        this.currentTime = this.startTime.split(' ')[1]
                    }else{
                        this.datetime = `${date} 00:00`
                    }
                }else{
                    this.datetime = `${date} ${this.datetime.split(' ')[1]}`
                    this.currentTime = this.datetime.split(' ')[1]
                }
            }
        },
        datetimeClick(){
            //日期视图里的时间点击事件
            if(this.datetime){
                this.timeShow=true;
                this.dateShow=false
            }
        },
        timeClick(data){
            if(this.type == 'time'){
                this.datetime = data.time
            }else{
                this.datetime = `${this.datetime.split(' ')[0]} ${data.time}`
            }
            if(data.close){
                this.pickerShow = false
            }
        },
        addZero(val){
            if(val < 10){
                return '0' + val
            }else{
                return val
            }
        },
        pickerHide(){
            if(this.pickerShow){
                this.pickerShow = false
            }
        },
        initRange(){
            if(this.range){
                this.startTime = this.start;
                this.endTime = this.end;
            }
        },
        clear(){
            this.datetime = ''
        }
    }
}
</script>

<style scoped type="text/css" lang="scss">
    .picker{
        width: 100%;
        height: 100%;
        position: relative;
        &>label{
            display: block;
            width: 100%;
            padding-left: 18px;
            box-sizing: border-box;
            height: 100%;
            position: relative;
            input{
                width: 100%;
                height: 100%;
                border: none;
                text-align: left;
                text-indent: 10px;
            }
            em{
                display: block;
                width: 17px;
                height: 100%;
                position: absolute;
                left: 0;
            }
        }
        
    }
    .dateTime-picker{
        width: 225px;
        height: 281px;
        border: 2px solid #6cd6df;
        position: absolute;    
        left: 0;
        background: #f5f6f8;
        top: 100%;
        &.time-picker-only{
            height: 236px;
            padding-top: 10px;
        }
        &.right{
            left: auto;
            right: 0;
        }
        .date-head{
            height: 25px;
            margin-top: 10px;
            &>div{
                float: left;
            }
            .date-prev,.date-next{
                width: 0;
                height: 0;
                border: 12px solid transparent;
                cursor: pointer;
            }
            .date-prev{
                border-right: 12px solid #575d69;
                &:hover{
                    border-right-color: #2fccdf
                }
            }
            .date-next{
                border-left: 12px solid #575d69;
                &:hover{
                    border-left-color: #2fccdf
                }
            }
            .date-center{
                width: 177px;
                height: 100%;
                padding: 0 12px;
                box-sizing: border-box;
                &>div{
                    float: left;
                    margin: 0 3px;
                }
                .date-year{
                    width: 81px;
                    height: 25px;
                }
                .date-month{
                    width: 60px;
                    height: 25px;
                }
            }
        }
    }
</style>
