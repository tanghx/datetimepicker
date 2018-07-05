<template>
    <div class="date-picker">
        <div class="datebody">
            <table>
                <thead>
                    <tr>
                        <th>日</th>
                        <th>一</th>
                        <th>二</th>
                        <th>三</th>
                        <th>四</th>
                        <th>五</th>
                        <th>六</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(week,index) in dayList" :key="index">
                        <td v-for="day in week" 
                            :key="day.id" 
                            :time="day.time" 
                            :class="{'day-item': day.id != null, 'active': day.id == currentDay, 'disabled': (startDate&&day.time<startDate)||(endDate&&day.time>endDate)}" 
                            @click="dateClick(day)">{{day.value}}</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div class="datefoot">
            <div class="footleft" v-if="type == 'both'">
                <em></em>
                <input type="text" v-model="time" @focus="timeClick">
            </div>
            <div class="footright">
                <a href="javascript:void(0)" @click="todayClick">今天</a>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    data () {
        return {
            time: '00:00',
            currentDay: null
        }
    },
    props: [ 'dayList', 'type', 'current', 'startTime', 'endTime' ],
    mounted(){
        if(this.current){
            this.initCurrent(this.current)
        }
    },
    watch: {
        current(nv){
            if(this.current){
                this.initCurrent(nv)
            }
        }
    },
    computed: {
        startDate(){
            if(this.startTime){
                let s = this.startTime.split(' ')[0].split('-')
                let t = new Date(s[0],s[1]-1,s[2])
                return t.getTime()
            }
        },
        endDate(){
            if(this.endTime){
                let e = this.endTime.split(' ')[0].split('-')
                let t = new Date(e[0],e[1]-1,e[2])
                return t.getTime()
            }
        }
    },
    methods:{
        dateClick(item){
            if(!item.id){
                return
            }
            let s = this.startDate,
                e = this.endDate,
                t = item.time,
                disabled = false
            if( ( s && t < s) || ( e && t > e ) ){
                return
            }
            if(( s && t == s) || ( e && t == e )){
                disabled = true
            }
            this.currentDay = item.id
            this.$emit('dateClick',{item:item,disabled:disabled})
        },
        todayClick(){
            let date = new Date()

            this.currentYear = date.getFullYear()
            this.currentMonth = date.getMonth() + 1
            this.currentDate = `${this.currentYear}-${this.currentMonth}-${date.getDate()}`

            let data = {
                id: this.currentDate,
                time: new Date(this.currentDate).getTime()
            }
            
            this.dateClick(data)
        },
        timeClick(){
            this.$emit('timeClick')
        },
        initCurrent(date){
            if(this.type == 'both'){
                let d = date.split(' ')[0].split('-')
                this.currentDay = `${d[0]}-${Number(d[1])}-${Number(d[2])}`
                this.time = date.split(' ')[1]
            }else{
                let d = date.split('-')
                this.currentDay = `${d[0]}-${Number(d[1])}-${Number(d[2])}`
                this.time = '00:00'
            }
        }
    }
}
</script>

<style scoped type="text/css" lang="scss">
.date-picker{
    .datebody{
        padding: 0 10px;
        table{
            border-collapse: collapse;
            border: none;
            thead{
                th{
                    width: 29px;
                    min-width: 29px;
                    height: 28px;
                    text-align: center;
                    font-weight: normal;
                    padding: 0px;
                }
            }
            tbody{
                td{
                    height: 27px;
                }
                .day-item{
                    text-align: center;
                    vertical-align: middle;
                    width: auto;
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
    }
    .datefoot{
        overflow: hidden;
        padding: 0 10px;
        margin-top: 10px;
        .footleft{
            float: left;
            width: 80px;
            height: 28px;
            border: 2px solid #c4c9d3;
            padding-left: 34px;
            box-sizing: border-box;
            position: relative;
            &>input{
                border: none;
                width: 100%;
                height: 100%;
                background: transparent;
                outline: none;
            }
            &>em{
                position: absolute;
                display: block;
                width: 15px;
                height: 100%;
                left: 9px;
                top: 0;
                background: url('../images/time.svg') center no-repeat;
            }
        }
        .footright{
            float: right;
            line-height: 28px;
            a{
                color: #00b8d3;
                text-decoration: none;
                &:hover{
                    text-decoration: underline;
                }
            }
        }
    }
}
</style>
