---
title: "逻辑应用限制和配置 | Microsoft Docs"
description: "适用于逻辑应用的服务限制和配置值的概述。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 47c13d5cbaeb114da18b710c52e2c989df4c0e01
ms.openlocfilehash: 2417d4e3d8033ff8d66ace8b27f398266a7fd101


---
# <a name="logic-app-limits-and-configuration"></a>逻辑应用限制和配置

以下是有关适用于 Azure 逻辑应用的当前限制和配置详细信息的信息。

## <a name="limits"></a>限制

### <a name="http-request-limits"></a>HTTP 请求限制

这些是针对单个 HTTP 请求和/或连接器调用的限制

#### <a name="timeout"></a>超时

|Name|限制|说明|
|----|----|----|
|请求超时|90 秒|[异步模式](app-service-logic-create-api-app.md)或 [Until 循环](app-service-logic-loops-and-scopes.md)可以根据需要进行补偿|

#### <a name="message-size"></a>消息大小

|Name|限制|说明|
|----|----|----|
|消息大小|50 MB|某些连接器和 API 可能不支持 50MB |
|表达式计算限制|131,072 个字符|`@concat()`、`@base64()`、`string` 不能超过此长度|

#### <a name="retry-policy"></a>重试策略

|Name|限制|说明|
|----|----|----|
|重试次数|4|可以使用[重试策略参数](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)进行配置|
|重试最大延迟|1 小时	|可以使用[重试策略参数](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)进行配置|
|重试最小延迟|20 秒|可以使用[重试策略参数](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)进行配置|

### <a name="run-duration-and-retention"></a>运行持续时间和保留期

这些是针对单个逻辑应用运行的限制。

|Name|限制|说明|
|----|----|----|
|运行持续时间|90 天||
|存储保留期|90 天|这从运行开始时间开始计算|
|最小重复间隔|1 秒|| 对于带有应用服务计划的逻辑应用为 15 秒
|最大重复间隔|500 天||


### <a name="looping-and-debatching-limits"></a>循环和解除批处理限制

这些是针对单个逻辑应用运行的限制。

|Name|限制|说明|
|----|----|----|
|ForEach 项|5,000|可以使用[查询操作](../connectors/connectors-native-query.md)根据需要筛选更大数组|
|Until 迭代|5,000||
|SplitOn 项|5,000||
|ForEach 并行度|20|可以通过将 `"operationOptions": "Sequential"` 添加到 `foreach` 操作来设置为顺序 foreach|


### <a name="throughput-limits"></a>吞吐量限制

这些是针对单个逻辑应用实例的限制。 

|Name|限制|说明|
|----|----|----|
|每秒触发数|100|可以根据需要在多个应用之间分配工作流|

### <a name="definition-limits"></a>定义限制

这些是针对单个逻辑应用定义的限制。

|Name|限制|说明|
|----|----|----|
|每个工作流的操作数|250|可以添加嵌套工作流以根据需要对此进行扩展|
|允许操作嵌套深度|5|可以添加嵌套工作流以根据需要对此进行扩展|
|每个订阅每个区域的流数|1000||
|每个工作流的触发数|10||
|每个表达式的最大字符数|8,192||
|最大 `trackedProperties` 大小（以字符为单位）|16,000|
|`action`/`trigger` 名称限制|80||
|`description` 长度限制|256||
|`parameters` 限制|50||
|`outputs` 限制|10||

### <a name="integration-account-limits"></a>集成帐户限制

这些是对添加到集成帐户的项目的限制

|Name|限制|说明|
|----|----|----|
|架构|8MB|可以使用 [blob URI](app-service-logic-enterprise-integration-schemas.md) 上传大于 2 MB 的文件 |
|映射（XSLT 文件）|2MB| |

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>B2B 协议（AS2、X12、EDIFACT）消息大小

这些是对 B2B 协议的限制

|Name|限制|说明|
|----|----|----|
|AS2|50MB|适用于解码和编码|
|X12|50MB|适用于解码和编码|
|EDIFACT|50MB|适用于解码和编码|

## <a name="configuration"></a>配置

### <a name="ip-address"></a>IP 地址

从[连接器](../connectors/apis-list.md)进行的调用来自下面指定的 IP 地址。

直接从逻辑应用进行的调用（即通过 [HTTP](../connectors/connectors-native-http.md) 或 [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)）可能来自任何 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)。

|逻辑应用区域|出站 IP|
|-----|----|
|澳大利亚东部|40.126.251.213|
|澳大利亚东南部|40.127.80.34|
|巴西南部|191.232.38.129|
|印度中部|104.211.98.164|
|美国中部|40.122.49.51|
|东亚|23.99.116.181|
|美国东部|191.237.41.52|
|美国东部 2|104.208.233.100|
|日本东部|40.115.186.96|
|日本西部|40.74.130.77|
|美国中北部|65.52.218.230|
|欧洲北部|104.45.93.9|
|美国中南部|104.214.70.191|
|亚洲东南部|13.76.231.68|
|印度南部|104.211.227.225|
|欧洲西部|40.115.50.13|
|印度西部|104.211.161.203|
|美国西部|104.40.51.248|


## <a name="next-steps"></a>后续步骤  

- 若要开始使用逻辑应用，请按照[创建逻辑应用](app-service-logic-create-a-logic-app.md)教程进行操作。  
- [查看常见示例和方案](app-service-logic-examples-and-scenarios.md)
- [使用逻辑应用可以自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694) 
- [了解如何将系统与逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


