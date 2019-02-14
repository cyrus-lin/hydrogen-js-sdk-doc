# 内容库操作

## 获取内容库详情

**接口**

`GET https://cloud.minapp.com/userve/v1/content/:content_group_id/`

其中 `content_group_id` 是内容库的 ID

**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.get('https://cloud.minapp.com/userve/v1/content/1/').then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 1,
  "name": "内容库",
  "acl_gids": [],
  "created_at": 1489137188,
  "updated_at": 1495769882
}
```

## 获取内容库列表

**接口**

`GET https://cloud.minapp.com/userve/v1/content/`

**提交参数**

- name 内容库名称等值查询查询

  例：查询内容库名称为 "内容库1" 的内容库

  `https://cloud.minapp.com/userve/v1/content/?name=内容库1`



**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.get('https://cloud.minapp.com/userve/v1/content/',{
  params: {
    name: '内容库1'
  }
}).then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "meta": {
    "limit": 20,
    "next": null,
    "offset": 0,
    "previous": null,
    "total_count": 1
  },
  "objects": [
    {
      "id": 1,
      "name": "内容库1",
      "acl_gids": [],
      "created_at": 1489137188,
      "updated_at": 1495769882
    }
  ]
}
```

## 创建内容库

**接口**

`POST https://cloud.minapp.com/userve/v1/content/`

**参数说明**

Content-Type: `application/json`

|   参数    |      类型     | 必填 | 说明 |
| :------- | :-----------  | :--- | :--- |
| name     | String        |  Y   | 内容库名称 |
| acl_gids | Integer Array |  N   | 用户的访问权限，其内为分组 ID |

**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.post('https://cloud.minapp.com/userve/v1/content/', {
  "name": "Content Group",
  "acl_gids": [1, 2]
}).then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 2,
  "name": "Content Group",
  "acl_gids": [1, 2],
  "created_at": 1489137188,
  "updated_at": 1495769882
}
```

**状态码说明**

`201`: 创建成功

`400`: 用户组 ID 不合法


## 编辑内容库

**接口**

`PUT https://cloud.minapp.com/userve/v1/content/:content_group_id/`


**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.put('https://cloud.minapp.com/userve/v1/content/2/', {
  "name": "Test Group"
}).then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 2,
  "name": "Test Group",
  "acl_gids": [1, 2],
  "created_at": 1489137188,
  "updated_at": 1495769882
}
```

**状态码说明**

`200`: 修改成功

`400`: 用户组 ID 不合法


## 删除内容库

**接口**

`DELETE https://cloud.minapp.com/userve/v1/content/:content_group_id/`


**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.delete('https://cloud.minapp.com/userve/v1/content/2/').then(res => {
  console.log(res.data)
})
```

**状态码说明**

`204`: 删除成功
