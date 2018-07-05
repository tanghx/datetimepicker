<template>
    <div class="dateTime">
        <div class="dateTime-input" :class="{'startAndEnd':options.range,'clearBtn':options.clearBtn}">
            <date-time-picker ref="picker" :type="options.type" :iconClass="options.type =='time'?'clock':'start'" :placeholder="options.range?options.startPlaceholder:options.placeholder" :end="endTime" :range="options.range" @timeInput="startInput"></date-time-picker>
            <date-time-picker ref="rangePicker" :type="options.type" :iconClass="'end'" :placeholder="options.endPlaceholder" :start="startTime" :range="options.range" v-if="options.range" @timeInput="endInput"></date-time-picker>
            <button v-if="options.clearBtn" @click="clear"></button>
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
import dateTimePicker from './dateTimePicker'

export default {
    data () {
        return {
            startTime: '',
            endTime: ''
        }
    },
    components:{
        dateTimePicker
    },
    props:[ 'options' ],
    methods:{
        startInput(time){
            this.startTime = time
        },
        endInput(time){
            this.endTime = time
        },
        clear(){
            this.$refs.picker.clear()
            this.$refs.rangePicker.clear()
        }
    }
}
</script>

<style scoped type="text/css" lang="scss">
    .dateTime{
        position: relative;
        width: 100%;
        height: 100%;
        border: 2px solid #c4c9d3;
        font-size: 12px;
        font-family: '微软雅黑';
        .dateTime-input{
            width: 100%;
            padding: 0 10px;
            height: 100%;
            box-sizing: border-box;
            &.startAndEnd /deep/ .picker{
                width: 50%;
                float: left;
            }
            & /deep/ .start{
                background: url('../images/laydate.svg') no-repeat center;
            }
            & /deep/ .end{
                background: url('../images/timeGo.svg') no-repeat center;
            }
            & /deep/ .clock{
                background: url('../images/time.svg') no-repeat center;
            }
            &.clearBtn{
                padding-right: 30px;
                button{
                    width: 30px;
                    height: 100%;
                    position: absolute;
                    border: none;
                    background: url('../images/X.svg') no-repeat center;
                    outline: none;
                    cursor: pointer;
                }
            }
        }
    } 
    
</style>
