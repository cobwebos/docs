---
title: 在 IoT Edge 上的实时视频分析中使用直接方法-Azure
description: IoT Edge 上的实时视频分析公开多种直接方法。 直接方法基于本主题中描述的约定。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: b87452de6b12b0335afca5e28abb3ef6adb29157
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260800"
---
# <a name="direct-methods"></a>直接方法

IoT Edge 上的实时视频分析公开了多个可从 IoT 中心调用的直接方法。 直接方法表示与设备进行的请求-答复式交互，类似于会立即成功或失败（在用户指定的超时时间后）的 HTTP 调用。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。 有关详细信息，请参阅[了解和调用 IoT 中心的直接方法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)。

本主题介绍这些方法和约定。

## <a name="conventions"></a>约定

直接方法基于以下约定：

1. 直接方法具有在主要版本中指定的版本。次要格式（如以下示例中所示）：

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

2. IoT Edge 模块上的实时视频分析的给定版本将支持所有直接方法（最新版本）。 例如，模块版本1.3 将支持版本为1.3、1.2、1.1 和1.0 的直接方法。
3. 所有直接方法都是同步的。
4. 错误结果遵循 OData 错误架构。
5. 名称应遵循以下限制：
    
    * 仅限字母数字字符和短划线，只要其不以破折号开头和结尾
    * 无空格
    * 最大值为32个字符

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

### <a name="top-level-error-codes"></a>顶级错误代码     

|状态 |代码   |Message|
|---|---|---|
|400|   BadRequest| 请求无效|
|400|   InvalidResource|    资源无效|
|400|   InvalidVersion| API 版本无效|
|402|   ConnectivityRequired    |Edge 模块需要云连接才能正常工作。|
|404|   NotFound    |未找到资源|
|409|   Conflict|   操作冲突|
|500|   InternalServerError|    内部服务器错误|
|503|   ServerBusy| 服务器忙|

### <a name="detailed-error-codes"></a>详细错误代码

详细验证错误，如图形模块验证，将添加为错误详细信息：

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
|409|   GraphTopologyInUse| 图形拓扑仍被一个或多个图形实例引用。|
|409|   OperationNotAllowedInState| 无法在当前状态中执行请求的操作。|
|409|   ResourceValidationError|    引用的资源（示例：资产）未处于有效状态。|

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
|一般用户错误    |400范围  ||
|找不到实体   |404        ||
|一般服务器错误| 500范围       ||

### <a name="graphtopologyset"></a>GraphTopologySet

如果没有具有给定名称或更新的现有拓扑，并且存在同名的现有拓扑，则创建单个拓扑。

关键方面：

* 拓扑可以自由更新，因为没有图形引用它。
* 如果所有引用关系图被停用，则可以自由更新拓扑，只要：

    * 新添加的参数有默认值
    * 所删除的参数未被任何图形引用
* 如果有活动图形，则不允许拓扑更新

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
已创建新实体  |201|   不适用|
一般用户错误 |400范围  ||
图形验证错误 |400    |GraphValidationError|
模块验证错误|   400 |ModuleValidationError|
一般服务器错误   |500范围  ||

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
|找不到实体|  204|    不适用|
|一般用户错误|   400范围   ||
|图形拓扑正在由一个或多个图形实例引用| 409 |GraphTopologyInUse|
|一般服务器错误| 500范围   ||

### <a name="graphtopologylist"></a>GraphTopologyList

检索与筛选条件匹配的所有图形拓扑的列表。

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
|$orderby|name  |升序|


#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|成功|   200 |空值|
|一般用户错误|   400范围   ||
|一般服务器错误| 500范围   ||

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
|一般用户错误|   400范围   ||
|找不到实体|  404 ||
|一般服务器错误| 500范围   ||

### <a name="graphinstanceset"></a>GraphInstanceSet

如果不存在具有指定名称或更新的现有关系图实例，且现有的现有名称相同，则创建一个。

关键方面：

* Graph 实例可以在 "已停用" 状态下自由更新。

    * 关系图在每次更新时重新验证。
* 当图形未处于 "非活动" 状态时，图形实例更新部分受到限制。
* 不允许在活动图形上更新图形实例。

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
|已创建新实体|    201 |不适用|
|一般用户错误|   400范围   ||
|图形验证错误    |400|   GraphValidationError|
|模块验证错误|  400 |ModuleValidationError|
|资源验证错误 |409    |ResourceValidationError|
|一般服务器错误  |500范围||    

### <a name="graphinstancedelete"></a>GraphInstanceDelete

删除单个图形实例。

关键方面：

* 只能删除已停用的关系图。

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
|找不到图形|   204|    不适用|
|一般用户错误    |400范围  ||
|图形未处于 "已停止" 状态    |409    |OperationNotAllowedInState|
|一般服务器错误| 500范围   ||

### <a name="graphinstancelist"></a>GraphInstanceList

这类似于 GraphTopologyList。 它允许使用来枚举图形实例。
检索与筛选条件匹配的所有关系图实例的列表。

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
|$orderby|  name|   升序|

#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
|成功    |200    |空值|
|一般用户错误|   400范围   ||
|一般服务器错误| 500范围   ||

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

激活单个图形实例。 

关键方面

* 当激活图形时，方法仅返回 
* Graph 假设激活时处于 "正在激活" 状态。

    * 关系图上的列表/获取操作会在适当状态返回关系图。
* 幂等性

    * 在 "激活" 状态启动关系图的行为方式与停用关系图的方式相同（即，在激活图形之前调用块）
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
|已创建新实体 |201|   不适用|
|一般用户错误    |400范围  ||
|模块验证错误   |400|   ModuleValidationError|
|资源验证错误|    409|    ResourceValidationError|
|图形处于停用状态 |409    |OperationNotAllowedInState|
|一般服务器错误| 500范围   ||

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

停用单个图形实例。 停用图形会使媒体处理正常停用，并确保每个 transiently 上存储的所有事件和媒体都提交给云，无论是否适用。

关键方面：

* 当停用图形时，方法仅返回
* Graph 在停用 "停用" 状态时将其取消。

    * 关系图上的列表/获取操作会在适当状态返回关系图。
    * 仅当所有媒体都已上传到云时，停止才会完成。
* 幂等性

    * 停用关系图时，"停用" 状态的行为方式与停用关系图的方式相同（即，在停用图形之前的调用块）
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
|已创建新实体 |201|   不适用|
|一般用户错误    |400范围  ||
|图形处于激活状态   |409|   OperationNotAllowedInState|
|一般服务器错误  |500范围  ||

## <a name="next-steps"></a>后续步骤

[模块克隆配置架构](module-twin-configuration-schema.md)
