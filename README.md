# datetimepicker

这是一个基于vue的日期时间选择器

## 用法
引入文件
```js
	import dateTime from 'datetimepicker'
```
使用
```js
	<date-time options="options"></date-time>
	...
	data () {
        return {
            config:{
				type: 'date',
                range: true,
                clearBtn: true,
                startPlaceholder: '开始时间',
                endPlaceholder: '结束时间'
			}
        }
    },
```

## 参数
#### type
类型：String
日期选择器的类型，'date'只有日期选择器，'time'只有时间选择器，'both'日期时间选择器

#### range
类型：Boolean
是否是范围选择，即开始时间和结束时间

#### clearBtn
类型：Boolean
是否显示清空按钮

#### placeholder
类型：String
非范围选择时的占位符

#### startPlaceholder
类型：String
范围选择时开始日期的占位符

#### endPlaceholder
类型：String
范围选择时结束日期的占位符