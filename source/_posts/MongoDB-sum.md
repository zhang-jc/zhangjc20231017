title: MongoDB中sum的方法
date: 2015-11-15 16:01:43
tags:
  - MongoDB
categories:
  - 数据库
  - MongoDB
---
MongoDB中求和的方法有以下两种：
### 聚合管道（Aggregation Pipelines）
以下是MongoDB官网的示例，很清楚。注意，用来统计的 field 的数据类型得是数值类型，不能是字符串，否则统计结果为 0。

![aggregation-pipeline](/uploads/aggregation-pipeline.png)

### Map-Reduce
以下是MongoDB官网的示例，也很清楚。注意，Map-Reduce的数据结果保存在一个新的Collection中；Collection的名字就是out的值。在该示例中即 order_totals。

![map-reduct](/uploads/map-reduce.png)

如果要 sum 的 field 需要数据类型转换，可以在 Map 阶段完成。如下数据：

	db.orders_str.insert({cust_id:"A123",amount:"500",status:"A"})
	db.orders_str.insert({cust_id:"A123",amount:"250",status:"A"})
	db.orders_str.insert({cust_id:"B212",amount:"200",status:"A"})
	db.orders_str.insert({cust_id:"A123",amount:"300",status:"D"})

Map-Reduce：

	db.orders_str.mapReduce(
		function(){
			var cnt = parseInt(this.amount);
			emit(this.cust_id,cnt);
		},
		function(key,values){
			return Array.sum(values);
		},
		{
			query:{status:"A"},
			out: "order_totals_str"
		}
	)

输出结果：

	db.order_totals_str.find({})

{ "_id" : "A123", "value" : 750 }  
{ "_id" : "B212", "value" : 200 }  
DONE！!
