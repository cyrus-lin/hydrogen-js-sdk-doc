# 新增数据记录

## 操作步骤

* 创建一个 `Table` 对象 `table`；
* 在 `table` 对象创建一条空记录；
* 为空记录赋值；
* 将创建的记录保存到服务器。

### 创建 `Table` 对象

通过 `tableName` 或 `tableId` 实例化一个 `Table` 对象，操作该对象即相当于操作对应的数据表，这里推荐用 tableName。

**示例代码**

{% tabs swift1="Swift", oc1="Objective-C" %}
{% content "swift1" %}
```
// 通过 tableId 创建数据表实例 
let table = Table(Id: 1236**)

// 通过 tablename 创建数据表实例
let table = Table(name: "Book")
```
{% content "oc1" %}
```
// 通过 tableId 创建数据表实例
BaaSTable *table = [[BaaSTable alloc] initId:1236**];

// 通过 tablename 创建数据表实例
BaaSTable *table = [[BaaSTable alloc] initWithName:@"Book"];
```
{% endtabs %}

**参数说明**

tableName 和 tableId 二选一

| 名称     | 类型   |说明    |
| :-----  | :----- | :--- |
| Id   | Int  |  数据表的 Id |
| name | String |   数据表名 |

### 本地创建一条空记录

{% tabs swift2="Swift", oc2="Objective-C" %}
{% content "swift2" %}
```
let record: Record = table.createRecord()
```
{% content "oc2" %}
```
BaaSRecord *record = [table createRecord];
```
{% endtabs %}

关于 `Record` 类型查看 [数据类型](./data-type.md) 章节

### 为上面创建的空记录赋值

有两种类型的赋值操作：

a.一次性赋值：

{% tabs swift3="Swift", oc3="Objective-C" %}
{% content "swift3" %}
```
record.set(record: ["name": "bookname", "color": "red", "price": 19])
```
**参数说明**

| 名称       | 类型           | 说明        |
| :-------- | :------------  | :------    |
| record    | Dictionary     | 记录信息，key 为字段名称   |
{% content "oc3" %}
```
[record setWithRecord:@{@"name": @"bookname", @"color": @"red", @"price": @10}];
```
**参数说明**

| 名称       | 类型           | 说明        |
| :-------- | :------------  | :------    |
| record    | NSDictionary   | 记录信息，key 为字段名称   |
{% endtabs %}

b.逐个赋值：

{% tabs swift4="Swift", oc4="Objective-C" %}
{% content "swift4" %}
```
record.set(key: "color", value: "red")
record.set(key: "price", value: 10)
```
{% content "oc4" %}
```
[record setWithKey:@"color" value:@"red"];
[record setWithKey:@"price" value:@10];
```
{% endtabs %}

> **info**
> 对同一字段进行多次 `set` 操作，后面的数据会覆盖掉前面的数据

### 将创建的记录保存到服务器

{% tabs swift5="Swift", oc5="Objective-C" %}
{% content "swift5" %}
```
record.save { (success, error) in

}
```
{% content "oc5" %}
```
[record save:^(BOOL success, NSError * _Nullable error) {

}];
```
{% endtabs %}

**返回结果**
 
| 名称      | 类型           | 说明 |
| :------- | :------------  | :------ |
| success  | Bool           | 是否新增数据成功 |
| error   |  NSError |  错误信息  |

success 写入数据成功后，**记录对象 record 的数据将被更新**。

error 对象结构请参考[错误处理和错误码](/ios-sdk/error-code.md)

**通过上面的四个步骤，即完成了一条记录的插入，具体操作阅读以下内容。**

## 添加普通数据

假设有一个 `Book` 表，包括 `name`、`author`、`price` 等字段，表示书名、作者、价格。

**请求示例**

{% tabs swift6="Swift", oc6="Objective-C" %}
{% content "swift6" %}
```
// 创建 `Table` 对象
let bookTable = Table(name: "Book")

// 创建一条空的记录
let book = bookTable.createRecord()

// 设置方式一
book.set(record: ["name": "老人与海", "author": "海明威", "price": 49])

// 设置方式二
book.set(key: "name", value: "老人与海");
book.set(key: "author", value: "海明威")
book.set(key: "price", value: 49)

book.save { (success, error) in

}
```
{% content "oc6" %}
```
// 创建 `Table` 对象
BaaSTable *bookTable = [[BaaSTable alloc] initWithName:@"Book"];

// 创建一条空记录
BaaSRecord *book = [bookTable createRecord];

// 设置方式一
[book setWithRecord:@{@"name": @"老人与海", @"author": @"海明威" @"price": 49}];

// 设置方式二
[book setWithKey:@"name" value:@"老人与海"];
[book setWithKey:@"author" value:@"海明威"];
[book setWithKey:@"price" value:49];

[book save:^(BOOL success, NSError * _Nullable error) {

}];
```
{% endtabs %}

## 添加日期时间 Date 类型的数据

数据表允许添加时间日期类型的列，为该类型的记录赋值，需要使用 ISO Date 格式的字符串，如 Book 表定义一个时间日期类型的列 publish_date，创建一条记录时，该字段的赋值操作如下：

{% tabs swift7="Swift", oc7="Objective-C" %}
{% content "swift7" %}
```
let dateISO = ISO8601DateFormatter().string(from: Date())
book.set(key: "publish_date", value: dateISO)
```
{% content "oc7" %}
```
NSISO8601DateFormatter *dateFormatter = [[NSISO8601DateFormatter alloc] init];
NSString *dateISO = [dateFormatter stringFromDate:[NSDate date]];
[book setWithKey:@"publish_date" value:dateISO];
```
{% endtabs %}

## 添加 file 类型数据

> **info**
>  为 file 类型字段设置值时，必须以 json 格式提供特定的文件信息。通过使用 `fileInfo` 可方便获取文件信息。

如 Book 表定义 file 类型的列 cover，表示书的封面。示例：将一个文件上传到知晓云后，将该文件设置为书的封面。

{% tabs swift8="Swift", oc8="Objective-C" %}
{% content "swift8" %}
```
let filePath = Bundle.main.path(forResource: "cover", ofType: "png")!
fileManager.upload(filename: "cover", localPath: filePath, categoryName: "Book", progressBlock: { progress in
                
    }) { (file, error) in
    book.set(key: "cover", value: file?.fileInfo)
}
```
{% content "oc8" %}
```
NSString *filePath = [[NSBundle mainBundle] pathForResource:@"cover" ofType:@"png"];
[fileManager uploadWithFilename:@"cover" localPath:filePath categoryName:@"Book" progressBlock:^(NSProgress * _Nullable progress) {

    } completion:^(BAASFile * _Nullable file, NSError * _Nullable error) {
        [book setWithKey:@"cover" value:file.fileInfo];
}];
```
{% endtabs %}

关于 `file` 类型查看 [文件](../file/file.md) 章节

<!--

## 添加 geojson 类型数据

表中有名称为 location，polygon 的两列，类型都为 geojson。

{% tabs swift8_1="Swift", oc8_1="Objective-C" %}
{% content "swift8_1" %}
```
// Point 类型
let point = GeoPoint(latitude: 2, longitude: 10)
record.set(key: "location", value: point.geoJson)

// GeoPolygon
let polygon = GeoPolygon(coordinates: [[30, 10], [40, 40], [20, 40], [10, 20], [30, 10]])
record.set(key: "polygon", value: polygon.geoJson)
```
{% content "oc8_1" %}
```
// Point类型
BAASGeoPoint *point = [[BAASGeoPoint alloc] initWithLatitude:10 longitude:2];
[record setWithKey:@"location" value:point.geoJson];

// GeoPolygon
BAASGeoPolygon *polygon = [[BAASGeoPolygon alloc] initWithCoordinates:@[@[30, 10], @[40, 40], @[20, 40], @[10, 20], @[30, 10]]];
[record setWithKey:@"polygon" value:polygon.geoJson];
```
{% endtabs %}

关于 geojson 类型查看 [地理位置操作](./geo.md) 章节

-->

## 添加 object 类型数据

对象内的属性名只能包含字母、数字和下划线，必须以字母开头，比如 `{$ifanr.x: 123}` 和 `{知晓云: "test"}` 是错误的

## 添加 array 类型数据

添加 array 类型数据的方法与添加其他类型数据的方法基本一致。区别在于，array 类型数据是将一个的数组赋值给某个字段。

array 类型数据中的元素类型，要与预先在知晓云平台设定的字段类型一致。否则创建的数据将不包含该 array 类型的字段。

## 添加 pointer 类型数据 

> **info**
> 每张表最多能建立 3 个 pointer 类型的字段。如有更多需求，请提交工单说明  
> pointer 指向的数据表，不能改名或删除

假设现在有一张 Book 表, Book 表部分字段如下:

| 字段名          | 字段类型          | 说明                 |
|----------------|------------------|----------------------|
| comment        |  pointer         | 指向了 `Comment` 表     |

现在在 Book 表中新增一条数据，其中: 

comment 字段指向了 Comment 表中 id 为 5bad87ab0769797b4fb27a1b 的数据行

{% tabs swift9="Swift", oc9="Objective-C" %}
{% content "swift9" %}
```
let comment = table.getWithoutData(recordId: "5bad87ab0769797b4fb27a1b")
book.set(key: "comment", value: comment)
}
```
{% content "oc9" %}
```
BaaSRecord *comment = [table getWithoutDataWithRecordId:@"5bad87ab0769797b4fb27a1b"];
[book setWithKey:@"comment" value:comment];
```
{% endtabs %}

## 批量新增数据项

{% tabs swift10="Swift", oc10="Objective-C" %}
{% content "swift10" %}
```
let options = ["enable_trigger": true]
table.createMany([["name": "书名六", "author": "hua", "price": 19], 
                  ["name": "书名七", "author": "lin", "price": 19]], options: options) { (success, error) in

}
```
{% content "oc10" %}
```
NSDictionary *options = @{@"enable_trigger": @YES};
[table createMany:@[@{@"name": @"bookname", @"price": @10}, 
                     @{@"name": @"bookname2", @"price": @11}] options:options completion:^(NSDictionary<NSString *,id> * _Nullable records, NSError * _Nullable error) {

}];
```
{% endtabs %}

**参数说明**

| 参数名    | 类型    | 说明              |  必填  |
|-----------|---------|-------------------|----|
| records   | Dictionary  |   符合表结构的对象| Y |
| options | Dictionary    |   批量操作选项 ，目前支持支持 enable_trigger, true 为触发触发器 |  N |

**返回结果**
 
| 名称      | 类型           | 说明 |
| :------- | :------------  | :------ |
| result  |  Dictionary           | 新增的数据结果 |
| error   |  NSError |  错误信息  |

> **info**
> error 为 nil 不说明批量写入数据完全成功，仅代表服务端已收到并处理了这个请求，只有当返回的结果中 operation_result 列表中不存在 error 元素时，才可以认为所有数据均写入成功。

 **返回示例**
 ```
 {
  "succeed": 10,
  "total_count": 10,
  "operation_result": [
    {"success": {"id": "5bfe000ce74243582bf2979f", "created_at": "1543459089"}},
    {
       "error": {
         "code": 11000,
         "err_msg": "数据写入失败，具体错误信息可联系知晓云微信客服：minsupport3 获取。"
       }
    }
  ]
}
 ```

**参数说明**
* succeed:	成功创建记录数
* total_count:	总的待创建记录数
* operation_result: 批量写入每一条数据的结果

error 对象结构请参考[错误处理和错误码](/ios-sdk/error-code.md)

**常见错误码**
* 201：成功写入
* 400：非法数据
* 403：无权限写入（表权限）