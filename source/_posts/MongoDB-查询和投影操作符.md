title: MongoDB 查询和投影操作符
tags:
  - MongoDB
  - 查询操作符
  - 投影操作符
categories:
  - 数据库
  - MongoDB
date: 2016-05-15 18:58:31
---

### 查询选择器
#### 比较操作符

想了解不同 BSON 类型值的比较，参见 [BSON比较顺序详细说明](https://docs.mongodb.org/manual/reference/bson-types/#bson-types-comparison-order)。

[$eq](https://docs.mongodb.com/manual/reference/operator/query/eq/#op._S_eq) 匹配跟指定值相等的值  
[$gt](https://docs.mongodb.com/manual/reference/operator/query/gt/#op._S_gt) 匹配比指定值大的值  
[$gte](https://docs.mongodb.com/manual/reference/operator/query/gte/#op._S_gte) 匹配比指定值大或者相等的值  
[$lt](https://docs.mongodb.com/manual/reference/operator/query/lt/#op._S_lt) 匹配比指定值小的值  
[$lte](https://docs.mongodb.com/manual/reference/operator/query/lte/#op._S_lte) 匹配比指定值小或者相等的值  
[$ne](https://docs.mongodb.com/manual/reference/operator/query/ne/#op._S_ne) 匹配跟指定值不相等的值  
[$in](https://docs.mongodb.com/manual/reference/operator/query/in/#op._S_in) 匹配在指定数组中的任意值  
[$nin](https://docs.mongodb.com/manual/reference/operator/query/nin/#op._S_nin) 匹配不在指定数组中的值

#### 逻辑操作符

[$or](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or) 用逻辑 **OR** 操作符连接查询子句，返回所有匹配任一个条件子句的文档。  
[$and](https://docs.mongodb.com/manual/reference/operator/query/and/#op._S_and) 用逻辑 **AND** 操作符连接查询子句，返回所有同时匹配两个条件子句的文档。  
[$not](https://docs.mongodb.com/manual/reference/operator/query/not/#op._S_not) 反转查询条件的效果，返回*不匹配*查询条件的文档。  
[$nor](https://docs.mongodb.com/manual/reference/operator/query/nor/#op._S_nor) 用逻辑 **NOR** 操作符连接查询子句，返回两个条件都匹配失败的所有文档。

#### 元素操作符

[$exists](https://docs.mongodb.com/manual/reference/operator/query/exists/#op._S_exists) 匹配有指定属性的文档。  
[$type](https://docs.mongodb.com/manual/reference/operator/query/type/#op._S_type) 选择属性是指定类型的文档。

#### 评估操作符

[$mod](https://docs.mongodb.com/manual/reference/operator/query/mod/#op._S_mod) 对一个属性的值执行模数运算，选择有指定结果的文档。  
[$regex](https://docs.mongodb.com/manual/reference/operator/query/regex/#op._S_regex) 选择值匹配指定规则表达式的文档。  
[$text](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text) 执行文本搜索。  
[$where](https://docs.mongodb.com/manual/reference/operator/query/where/#op._S_where) 匹配满足一个 JavaScript 表达式的文档。

#### 空间操作符

[$geoWithin](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin) 选择包围 [GeoJSON  几何图形](https://docs.mongodb.com/manual/reference/geojson/#geospatial-indexes-store-geojson) 的几何图形。[二维球形](https://docs.mongodb.com/manual/core/2dsphere/)和[二维索引](https://docs.mongodb.com/manual/core/2d/)支持 [$geoWithin](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)。  
[$geoIntersects](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects) 选择与一个 [GeoJSON 几何图形](https://docs.mongodb.com/manual/reference/geojson/#geospatial-indexes-store-geojson) 相交的几何图形。[二维球形](https://docs.mongodb.com/manual/core/2dsphere/)支持 [$geoIntersects](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects)。  
[$near](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near) 返回跟一个点邻近的空间对象。需要一个空间索引。[二维球形](https://docs.mongodb.com/manual/core/2dsphere/)和[二维索引](https://docs.mongodb.com/manual/core/2d/)支持 [$near](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)。  
[$nearSphere](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere) 返回球形上跟一个点邻近的空间对象。[二维球形](https://docs.mongodb.com/manual/core/2dsphere/)和[二维索引](https://docs.mongodb.com/manual/core/2d/)支持 [$nearSphere](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)。

#### 数组操作符

[$all](https://docs.mongodb.com/manual/reference/operator/query/all/#op._S_all) 匹配查询中指定的所有元素的数组。  
[$elemMatch](https://docs.mongodb.com/manual/reference/operator/query/elemMatch/#op._S_elemMatch) 选择那些属性匹配所有 [$elemMatch](https://docs.mongodb.com/manual/reference/operator/query/elemMatch/#op._S_elemMatch) 条件指定的全部属性的数组。  
[$size](https://docs.mongodb.com/manual/reference/operator/query/size/#op._S_size) 选择符合指定属性个数的数组。

#### 位运算操作符

[$bitsAllSet](https://docs.mongodb.com/manual/reference/operator/query/bitsAllSet/#op._S_bitsAllSet) 匹配的数字或二进制值，其中这组位的位置都有一个值为1。  
[$bitsAnySet](https://docs.mongodb.com/manual/reference/operator/query/bitsAnySet/#op._S_bitsAnySet) 匹配的数字或二进制值，其中这组位的任意一个位置有一个值为1。  
[$bitsAllClear](https://docs.mongodb.com/manual/reference/operator/query/bitsAllClear/#op._S_bitsAllClear) 匹配的数字或二进制值，其中这组位的位置都有一个值为0。  
[$bitsAnyClear](https://docs.mongodb.com/manual/reference/operator/query/bitsAnyClear/#op._S_bitsAnyClear) 匹配的数字或二进制值，其中这组位的任意一个位置有一个值为0。

#### 注释操作符

[$comment](https://docs.mongodb.com/manual/reference/operator/query/comment/#op._S_comment) 给查询断言添加注释。

### 投影操作符

[$](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_) 展现第一个元素匹配查询条件的数组。  
[$elemMatch](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch) 展现第一个元素匹配指定的 [$elemMatch](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch) 条件的数组。  
[$meta](https://docs.mongodb.com/manual/reference/operator/projection/meta/#proj._S_meta) 展现 [$text](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text) 操作中赋值给文档的分数。  
[$slice](https://docs.mongodb.com/manual/reference/operator/projection/slice/#proj._S_slice) 限制从一个数组展示的元素的个数。支持 skip 和 limit。
