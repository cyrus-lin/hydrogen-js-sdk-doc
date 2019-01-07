# 文件操作

{% tabs first="SDK 1.1.2 及以上版本", second="SDK 1.1.2 以下版本" %}

{% content "first" %}

## SDK 1.1.2 及以上版本

实例化一个 `wx.BaaS.File` 对象，以下操作都是在该对象上进行操作，如下进行实例化：

`let MyFile = new wx.BaaS.File()`

### 文件上传

`MyFile.upload(fileParams, metaData)`

**fileParams 参数说明（必须）**

| 参数     |  类型   | 必填 | 说明 |
| :------- | :----- | :-- | :-- |
| filePath | String |  Y  | 本地资源路径 |

**metaData 参数说明（可选）**

| 参数          |  类型  | 必填 | 说明 |
| :----------- | :----- | :-- | :-- |
| categoryID   | String |  N  | 要上传的文件分类 ID |
| categoryName | String |  N  | 要上传的文件分类名 |

> **info**
> 请勿同时填写 categoryID 和 categoryName，默认只使用 categoryID

**返回参数说明**

res.data:

|   参数  | 类型   | 说明 |
| :----- | :----- | :-- |
| status | String | 成功返回 'ok' |
| path   | String | 上传后的文件地址 |
| file   | Object | 包含文件详细信息，详见以下 |

file 参数说明：

| 参数        |  类型  | 说明 |
| :--------- | :----- | :------ |
| cdn_path   | String | 文件在 cdn 上的路径 |
| created_at | String | 文件上传时间 |
| id         | Object | 文件 ID |
| mime_type  | String | 文件媒体类型 |
| name       | String | 文件名 |
| size       | Number | 以字节为单位 |

**示例代码**

```js
wx.chooseImage({
  success: function(res) {
    let MyFile = new wx.BaaS.File()
    let fileParams = {filePath: res.tempFilePaths[0]}
    let metaData = {categoryName: 'SDK'}

    MyFile.upload(fileParams, metaData).then(res => {
      /*
       * 注: 只要是服务器有响应的情况都会进入 success, 即便是 4xx，5xx 都会进入这里
       * 如果上传成功则会返回资源远程地址,如果上传失败则会返回失败信息
       */

      let data = res.data  // res.data 为 Object 类型
    }, err => {

    })
  }
})
```

> **danger**
> 使用 `wx.uploadFile` 以及 `SDK v.1.1.2` 之前版本的 `wx.BaaS.uploadFile` 返回的 res.data 是 json string 类型，而这里的 res.data 是 Object 类型，因此不需要再做类型转换了


#### 监听上传进度变化事件和中断上传任务 (SDK >= 1.8.0)
在 1.1.2 版本的基础上，1.8.0 版本中增加了对 [UploadTask](https://developers.weixin.qq.com/miniprogram/dev/api/UploadTask.html) 的支持， `upload` API 返回的 Promise 对象上增加了 `onProgressUpdate` 和 `abort` 方法，使文件上传增加了以下两个特性：

- 监听上传进度：`onProgressUpdate(callback)`
- 中断上传任务：`abort()`

callback 接收一个对象类型的参数，其结构如下：

| 参数                     |  类型  | 说明 |
| :------------------------| :----- | :------ |
| progress                 | Number | 上传进度百分比	 |
| totalBytesSent           | Number | 已经上传的数据长度，单位 Bytes	|
| totalBytesExpectedToSend | Number | 预期需要上传的数据总长度，单位 Bytes |


**示例代码**

```js
wx.chooseImage({
  success: function(res) {
    let MyFile = new wx.BaaS.File()
    let fileParams = {filePath: res.tempFilePaths[0]}
    let metaData = {categoryName: 'SDK'}

    // upload API 返回一个 Promise，1.8.0 后返回值增加了 onProgressUpdate 和 abort 方法
    let uploadTask =  MyFile.upload(fileParams, metaData)
    
    // 文件成功上传的回调
    uploadTask.then(res=>{
      
    })

    // 监听上传进度    
    uploadTask.onProgressUpdate(e => {
      console.log(e)
    })
    
    // 600 毫秒后中断上传
    setTimeout(()=> uploadTask.abort(), 600)
  }
})
```

**onProgressUpdate 返回示例**

```json
{
  "progress":80,
  "totalBytesSent":1507328,
  "totalBytesExpectedToSend":1883803
}
```

> **info**
> file 字段可用于含有 file 类型的数据表的数据操作，详细见 [新增数据项](../schema/create-record.md)


### 获取文件详情

`MyFile.get(fileID)`

**参数说明**

|  参数  |  类型   | 必填 | 说明 |
| :----- | :-- -- | :-- | :-- |
| fileID | String |  Y  | 文件 id |

**返回参数说明**

res.data:

| 参数        | 类型   | 说明 |
| :--------- | :----- | :------ |
| category   | Object | 包含文件分类信息，详见以下 |
| created_at | String | 文件上传时间 |
| id         | Object | 文件 ID |
| mime_type  | String | 文件媒体类型 |
| name       | String | 文件名 |
| path       | String | 文件在 cdn 上的路径 |
| size       | Number | 以字节为单位 |

category 参数说明：

| 参数  | 类型   | 说明 |
| :--- | :----- | :-- |
| id   | String | 分类 ID |
| name | String | 分类名 |

**示例代码**

```js
let MyFile = new wx.BaaS.File()
MyFile.get('5a2fe93308443e313a428c4f').then((res) => {
  // success
}, err => {
  // err
})
```

**返回示例**

```json
{
  "category": {
    "id": "5a2fe91508443e3123dbe1cb",
    "name": "科技"
  },
  "created_at": 1507822469,
  "id": "5a2fe93308443e313a428c4f",
  "mime_type": "image/png",
  "name": "sdk-test-minapp2.png",
  "path": "https://cloud-minapp-7894.cloud.ifanrusercontent.com/1eOledhCbvjgaCSE.png",
  "size": 3879
}
```


### 删除文件

`MyFile.delete(fileID)`

**参数说明**

| 参数    | 类型                   | 必填 | 说明 |
| :----- | :--------------------- | :-- | :-- |
| fileID | String or String Array | Y   | 文件 id (可为数组) |

**示例代码**

```js
let MyFile = new wx.BaaS.File()

MyFile.delete('5a2fe93308443e313a428c4f').then()

MyFile.delete(['5a2fe93308443e313a428c4c', '5a2fe93308443e313a428c4d']).then()
```

> **info**
> 删除单个文件，如果权限不足，会返回 401；删除多个文件，如果权限不足，则直接跳过该文件


### 查询，获取文件列表

文件查询与[数据表查询](../schema/query.md)方法一致，但只支持以下指定字段的筛选

| 支持字段       |  类型   | 说明 |
| :-----------  | :----- | :--- |
| id            | String | 文件 id |
| name          | String | 文件名 |
| size          | Integer| 文件大小，以字节为单位 |
| category_id   | String | 文件分类 id |
| category_name | String | 文件分类名 |
| created_at    | Integer| 创建时间 （格式为 unix 时间戳） |

**示例代码**

```js
let MyFile = new wx.BaaS.File()

// 查找所有文件
MyFile.find()

let query = new wx.BaaS.Query()
// 查询某一文件分类下的所有文件
query.compare('category_name', '=', categoryName)
// 查询文件名包含指定字符串的文件
query.contains('name', substr)


MyFile.setQuery(query).find()
```


```js
let MyFile = new wx.BaaS.File()

// 查找所有文件
MyFile.find()

// 按创建时间范围查询: 2018年10月24日17时10分57秒 至今上传的文件
let query = wx.BaaS.Query.and(new wx.BaaS.Query().compare('created_at', '<=', Math.ceil(Date.now() / 1000)), new wx.BaaS.Query().compare('created_at', '>=', 1540372257))

MyFile.setQuery(query).find()
```


### 排序
文件查询排序与[数据表排序](../schema/limit-and-order.md)方法一致，但只支持对以下指定字段进行排序

| 支持字段       |  类型   | 说明 |
| :-----------  | :----- | :--- |
| name          | String | 文件名 |
| size          | Number | 文件大小，以字节为单位 |
| created_at    | Number | 文件上传时间 |

**示例代码**

```js
let MyFile = new wx.BaaS.File()
MyFile.orderBy('-created_at').find().then()
```

### 分页
文件查询排序与[数据表分页](../schema/limit-and-order.md)方法一致

**示例代码**

```js
let MyFile = new wx.BaaS.File()
MyFile.limit(10).offset(5).find().then()
```

**返回示例**

成功时 res 结构如下
```json
 {
  "meta": {
    "limit": 20,
    "next": "/dserve/v1.3/uploaded-file/?limit=20&offset=20&where=%7B%22%24and%22%3A%5B%7B%22category_name%22%3A%7B%22%24eq%22%3A%22%E5%9B%BE%E7%89%87%22%7D%7D%5D%7D",
    "offset": 0,
    "previous": null,
    "total_count": 36
  },
  "objects": [{
    "category": {"id": "5b73f36f2a4f56246e76b7b3", "name": "图片"},
    "created_at": 1534823603,
    "id": "5b7b8cb3839c611ab4eb2599",
    "mime_type": "image/jpeg",
    "name": "wxc6b86e382a1e3294.o6zAJs5dCuYRqqJOq0MwNPlGiFVM.CGLDGRT03IsI7fa51717abe74ed34e0c9cc77dbe7079.jpg",
    "path": "https://cloud-minapp-11033.cloud.ifanrusercontent.com/1frxjPBNFAOrQtOS.jpg",
    "size": 11189
  }]
}
```

### 图片云处理

利用 CDN 图片云处理，可以快速便捷地完成图片缩放、裁切、打水印等操作，示例如下：

```
// 缩放图片至 400x400
https://cloud-minapp-7894.cloud.ifanrusercontent.com/1eRuaPvwdleauqyZ.jpg!/both/400x400

// 在图片右下角添加 “知晓云” 文字水印
https://cloud-minapp-7894.cloud.ifanrusercontent.com/1eiuEUuISgOstoVZ.png!/watermark/align/southeast/text/55+l5pmT5LqRCg==
```

具体用法和更多功能可查看文档：[如何通过图片 URL 进行图片云处理？](http://support.minapp.com/hc/kb/article/1082737/)

{% content "second" %}

## SDK 1.1.2 以下版本

### 文件上传

`wx.BaaS.uploadFile(fileParams)`

**fileParams 参数说明**

| 参数      | 类型   | 必填 | 说明 |
| :------- | :----- | :-- | :--- |
| filePath | String |  Y  | 本地资源路径 |

**返回参数说明**

这里效仿微信的 `wx.uploadFile` 接口，放回 json string 的 data，经过 JSON parse 后的数据结构如下：

| 参数    | 类型   | 说明 |
| :----- | :----- | :----- |
| status | String | 成功返回 'ok' |
| path   | String | 上传后的文件地址 |
| file   | Object | 包含文件详细信息，详见以下 |


> **danger**
> file 字段只在 <span style='color:red'> SDK version >= v1.1.1</span> 中出现

file 参数说明：

| 参数        |  类型  | 说明 |
| :--------- | :----- | :------ |
| cdn_path   | String | 文件在 cdn 上的路径 |
| created_at | String | 文件上传时间 |
| id         | Object | 文件 ID |
| mime_type  | String | 文件媒体类型 |
| name       | String | 文件名 |
| size       | Number | 以字节为单位 |

> **info**
> file 可用于含有 file 类型的数据表的数据操作，详细见 [新增数据记录](../schema/create-record.md)

**示例代码**

```js
wx.chooseImage({
  success: function(res) {
    let fileParams = {filePath: res.tempFilePaths[0]}

    wx.BaaS.uploadFile(fileParams).then(res => {
      /*
      * 注: 只要是服务器有响应的情况都会进入 success, 即便是 4xx，5xx 都会进入这里
      * 如果上传成功则会返回资源远程地址,如果上传失败则会返回失败信息
      */

      let data = JSON.parse(res.data) // res.data 为 JSON String 类型
    }, err => {

    })
  }
})
```

**返回示例**

JSON.parse(res.data)
```json
{
  "status": "ok",
  "path": "https://cloud-minapp-1131.cloud.ifanrusercontent.com/1e2fVFaWoaoAZPyr.svg",
  "file": {
    "cdn_path": "1e2fVFaWoaoAZPyr.svg",
    "created_at": 1507822469,
    "id": "59df8b852ab80e3656cf8783",
    "mime_type": "text/plain; charset=utf-8",
    "name": "tmp_262601706o6zAJs-pmaywKzqHIvzwU97rtiGIe4dd39171563993cf10b12bae2ac30ec.svg",
    "size": 3879
  }
}
```

> **info**
> 微信开发者工具**录音**结束后生成的是 base64 格式文本文件，而在真机上生成的是正常的 buffer。如果在开发者工具里上传录音文件，实际上传的会是一个 base64 格式的文本文件。因此，如果你在使用知晓云上传录音文件，请在真机上调试。该问题微信团队已知，并在修复当中。

{% endtabs %}
