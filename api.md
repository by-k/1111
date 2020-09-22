# 华尊对外数据接口文档

此文档接口是“`深圳市华尊科技股份有限公司`”对外部提供的一套数据共享接口。
所有接口涉密，请勿将接口`文档、账号、密码`等提供给他方使用。

## 一、请求规范

### 协议：HTTP1.1

### 接口风格：
如未明确说明，接口均采用`POST`方法请求，并把所需的参数以`JSON`格式放入请求体中，
请求头中需指定 `Content-type: application/json`

### 请求频率
防止客户端逻辑异常，导致循环调用，请求频率限制为`10次/s`

## 二：授权认证
针对有些需要认证的接口，先请求`登录`接口，然后将获取到的字符串放入请求头中，属性名为`token`。
如请求过车记录获取接口：
```http request
POST /v1/carpass HTTP/1.1
Host: localhost:8080
Content-type: application/json
token: xxxxxxxx
```

## 

## 三：服务器地址和授权账号
接口服务器：http://68.65.22.24:8892/
账号：harzone，密码：data@123

## 四：响应格式
请求正确响应示例：
```json
{
    "code": 0,
    "message": "",
    "data": null
}
```
请求异常响应示例：
```json
{
    "code": -10001,
    "message": "时间格式应为：yyyy-MM-dd HH:mm:ss",
    "data": null
}
```
- `code`为 0 表示成功，其他值表示失败
- `message`为`code`的中文说明
- `data`为返回的数据

## 五：接口列表
1. 登录

请求：
```http request
POST /v1/sys/login
Content-type: application/json

{
    "name": "123",
    "pwd": "567"
}

```
| 属性 | 解释 | 数据类型 | 是否必须 |
| ---- | ---- | ---- | ---- |
| name | 用户名 | string | 是 |
| pwd | 密码 | string | 是 |

响应：
```json
{
    "code": 0,
    "message": "",
    "data": "xxxxxx" 
}
```
data中返回的是授权需要的token字符串

2. 获取过车数据

请求：
```http request
POST /v1/carpass
token: xxxx
Content-type: application/json

{
    "plateNos": ["粤B888888","粤B666666"],
    "startTime": "2020-01-01 00:00:00",
    "endTime": "2020-02-01 00:00:00"
}

```
| 属性 | 解释 | 数据类型 | 是否必须 | 备注 |
| ---- | ---- | ---- | ---- | ---- |
| plateNos | 车牌号 | array | 是 | 最大支持100个 |
| startTime | 查询开始时间 | string | 是 | 时间格式为：yyyy-MM-dd HH:mm:ss |
| endTime | 查询结束时间 | string | 是 | 时间格式为：yyyy-MM-dd HH:mm:ss，最大时间范围为1个月 |
| vehicleType | 车型 | string | 否 |  |

车型枚举为：
> 1小汽车,2面包车,3货车,4客车,5SUV,6大货车,7大客车,8未知

响应：
```json
{
    "code": 0,
    "message": "",
    "data": [{
        "plateNo":"粤B888888",
        "passTime": "2020-01-01 12:00:00",
        "checkpointId": "440300120110000123",
        "checkpointName": "莲塘口岸负二楼停车场入口"
    }]
}
```
| 属性 | 解释 | 数据类型 | 备注 |
| ---- | ---- | ---- | ---- |
| plateNo | 车牌号 | string |  |
| passTime | 过车时间 | string | 时间格式为：yyyy-MM-dd HH:mm:ss |
| checkpointId | 卡口ID | string |  |
| checkpointName | 卡口名称 | string |-|

