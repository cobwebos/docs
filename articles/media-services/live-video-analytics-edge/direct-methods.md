---
title: 在 IoT Edge 上的实时视频分析中使用直接方法 - Azure
description: IoT Edge 上的实时视频分析公开了多种直接方法。 直接方法基于本主题中描述的约定。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ed7cec7b8513044c2bf9b24600b8d9f42a485aae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091821"
---
# <a name="direct-methods"></a>直接方法

IoT Edge 上的实时视频分析公开了可以从 IoT 中心调用的多种直接方法。 直接方法表示与设备进行的请求-答复式交互，类似于会立即成功或失败（在用户指定的超时时间后）的 HTTP 调用。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。 有关详细信息，请参阅[了解直接方法并从 IoT 中心进行调用](../../iot-hub/iot-hub-devguide-direct-methods.md)。

本主题介绍这些方法和约定。

## <a name="conventions"></a>约定

直接方法基于以下约定：

1. 直接方法具有以 MAJOR.MINOR 格式指定的版本（如以下示例所示）：

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. IoT Edge 模块上实时视频分析的给定版本支持直至其当前版本的所有直接方法。 例如，模块版本 1.3 将支持版本 1.3、1.2、1.1 和 1.0 版本的直接方法。
3. 所有直接方法都是同步的。
4. 错误结果遵循 OData 错误架构。
5. 名称应遵守以下约束：
    
    * 仅限字母数字字符和短划线，不能以短划线开头和结尾
    * 不含空格
    * 最多 32个字符

### <a name="example"></a>示例

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>顶层错误代码     

|状态 |代码   |Message|
|---|---|---|
|400|   BadRequest| 请求无效|
|400|   InvalidResource|    资源无效。|
|400|   InvalidVersion| API 版本无效|
|402|   ConnectivityRequired    |Edge 模块需要云连接才能正常工作。|
|404|   NotFound    |未找到资源|
|409|   Conflict|   操作冲突|
|500|   InternalServerError|    内部服务器错误|
|503|   ServerBusy| 服务器繁忙|

### <a name="detailed-error-codes"></a>详细错误代码

添加了详细的验证错误（如图形模块验证）作为错误详细信息：

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|状态|    详细代码   |说明|
|---|---|---|
|400|   GraphValidationError|   常规图形错误，如循环或分区等。|
|400|   ModuleValidationError|  模块特定的验证错误。|
|409|   GraphTopologyInUse| 一个或多个图形实例仍在引用图形拓扑。|
|409|   OperationNotAllowedInState| 无法在当前状态下执行请求的操作。|
|409|   ResourceValidationError|    引用的资源（示例：资产）处于无效状态。|

## <a name="details"></a>详细信息  

### <a name="graphtopologyget"></a>GraphTopologyGet

此直接方法检索单个图形拓扑。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已找到实体|  200 |空值
|一般用户错误    |400 range  ||
|找不到实体   |404        ||
|一般服务器错误| 500 range       ||

### <a name="graphtopologyset"></a>GraphTopologySet

如果不存在具有给定名称或更新的拓扑，并且不存在同名的现有拓扑，则创建一个拓扑。

关键方面：

* 如果没有图形引用拓扑，则可以自由更新拓扑。
* 如果停用所有引用关系图，只要满足以下情况，就可以自由更新拓扑：

    * 新添加的参数具有默认值
    * 任何图形均未引用已删除的参数
* 如果存在活动图，则不允许拓扑更新

#### <a name="request"></a>请求

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
已更新现有实体 |200|   空值|
已创建新的实体  |201|   空值|
一般用户错误 |400 range  ||
图形验证错误 |400    |GraphValidationError|
模块验证错误|   400 |ModuleValidationError|
一般服务器错误   |500 range  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

删除单个图形拓扑。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已删除实体|    200|    空值|
|找不到实体|  204|    空值|
|一般用户错误|   400 range   ||
|一个或多个图形实例正在引用图形拓扑| 409 |GraphTopologyInUse|
|一般服务器错误| 500 range   ||

### <a name="graphtopologylist"></a>GraphTopologyList

检索所有符合筛选条件的图形拓扑的列表。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>筛选器支持

|操作      |字段   |运算符|
|---|---|---|
|$orderby|name  |asc|


#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|Success|   200 |空值|
|一般用户错误|   400 range   ||
|一般服务器错误| 500 range   ||

### <a name="graphinstanceget"></a>GraphInstanceGet

检索单个图形实例：

#### <a name="request"></a>请求

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已找到实体   |200|   空值|
|一般用户错误|   400 range   ||
|找不到实体|  404 ||
|一般服务器错误| 500 range   ||

### <a name="graphinstanceset"></a>GraphInstanceSet

如果不存在具有给定名称或更新的图形实例，并且不存在同名的现有图形实例，则创建单个图形实例。

关键方面：

* 图形实例处于“已停用”状态时可以自由更新。

    * 每次更新时都会重新验证图形。
* 图形不处于“非活动”状态时，图形实例更新受到部分限制。
* 不允许对活动图形更新图形实例。

#### <a name="request"></a>请求

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>响应

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已更新现有实体    |200    |空值|
|已创建新的实体|    201 |空值|
|一般用户错误|   400 range   ||
|图形验证错误    |400|   GraphValidationError|
|模块验证错误|  400 |ModuleValidationError|
|资源验证错误 |409    |ResourceValidationError|
|一般服务器错误  |500 range||    

### <a name="graphinstancedelete"></a>GraphInstanceDelete

删除单个图形实例。

关键方面：

* 只能删除已停用的图形。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已成功删除图形|    200|    空值|
|找不到图形|   204|    空值|
|一般用户错误    |400 range  ||
|图形不处于“已停止”状态    |409    |OperationNotAllowedInState|
|一般服务器错误| 500 range   ||

### <a name="graphinstancelist"></a>GraphInstanceList

这类似于 GraphTopologyList。 它可以用来枚举图形实例。
检索所有符合筛选条件的图形实例的列表。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>筛选器支持

|操作  |   字段|   运算符|
|---|---|---|
|$orderby|  name|   asc|

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|Success    |200    |空值|
|一般用户错误|   400 range   ||
|一般服务器错误| 500 range   ||

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

激活单个图形实例。 

关键方面

* 方法仅在激活图形后返回 
* Graph 假设激活时处于 "正在激活" 状态。

    * 对图形进行 List/Get 操作将返回处于正确状态的图形。
* 幂等性：

    * 在 "激活" 状态启动关系图的行为方式与停用关系图的方式相同 (即：调用块直到激活 graph) 
    * 以 "活动" 状态激活关系图会立即成功返回。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已成功激活图形   |200    |空值|
|已创建新的实体 |201|   空值|
|一般用户错误    |400 range  ||
|模块验证错误   |400|   ModuleValidationError|
|资源验证错误|    409|    ResourceValidationError|
|图形处于“正在停用”状态 |409    |OperationNotAllowedInState|
|一般服务器错误| 500 range   ||

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

停用单个图形实例。 停用图形可以适当地停用媒体处理，并确保在适当的情况下将临时存储在边缘上的所有事件和媒体都提交到云中。

关键方面：

* 方法仅在停用图形后返回
* Graph 在停用 "停用" 状态时将其取消。

    * 对图形进行 List/Get 操作将返回处于正确状态的图形。
    * 停止仅在所有媒体都已上传到云后完成。
* 幂等性：

    * 停用关系图时，"停用" 状态的行为方式与停用关系图的方式相同 (即：调用块直到激活 graph) 
    * 停用处于 "非活动" 状态的关系图会立即成功返回。

#### <a name="request"></a>请求

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```

|条件  |状态代码    |详细错误代码|
|---|---|---|
|已成功激活图形   |200|   空值|
|已创建新的实体 |201|   空值|
|一般用户错误    |400 range  ||
|图形处于“正在激活”状态   |409|   OperationNotAllowedInState|
|一般服务器错误  |500 range  ||

## <a name="next-steps"></a>后续步骤

[模块孪生配置架构](module-twin-configuration-schema.md)
