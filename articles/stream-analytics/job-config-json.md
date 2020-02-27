---
title: Azure 流分析 JobConfig 字段
description: 本文列出了用于在 Visual Studio Code 中创建作业的 Azure 流分析 JobConfig 文件的支持字段。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617952"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 流分析 JobConfig 字段

*JobConfig*文件支持以下字段，这些字段用于[使用 Visual Studio Code 创建 Azure 流分析作业](quick-create-vs-code.md)。

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|名称|类型|必选|值|
|----|----|--------|-----|
|DataLocale|字符串|否|流分析作业的数据区域设置。 值应为受支持的的名称。 如果未指定，则默认为 "en-us"。|
|OutputErrorPolicy|字符串|否|指示要应用于出现错误的事件的策略，该策略不能写入外部存储（缺少列值、错误类型或大小的列值）。 -停止或删除|
|EventsLateArrivalMaxDelayInSeconds|integer|否|最大可容忍延迟时间，以秒为单位。 支持的范围为-1 到1814399（20.23：59：59天），-1 用于指定无限期等待。 如果该属性不存在，则将其解释为具有值-1。|
|EventsOutOfOrderMaxDelayInSeconds|integer|否|可按顺序将有序事件调整为按顺序返回的最大可容忍延迟（以秒为单位）。|
|EventsOutOfOrderPolicy|字符串|否|指示要应用于输入事件流中未按顺序到达的事件的策略。 -调整或删除|
|StreamingUnits|integer|是|指定流式处理作业使用的流式处理单位数。|
|CompatibilityLevel|字符串|否|控制流式处理作业的某些运行时行为。 -可接受的值为 "1.0"、"1.1"、"1.2"|
|UseSystemAssignedIdentity|boolean|否|设置为 true 可使此作业使用托管 Azure Active Directory 标识作为其自身与其他 Azure 服务进行通信。|
|GlobalStorage|字符串|否|全局存储帐户用于存储与流分析作业相关的内容，例如 SQL 引用数据快照。|
|GlobalStorage. AccountKey|字符串|否|全局存储帐户的相应键。|
|DataSourceCredentialDomain|字符串|否|凭据本地存储的保留属性。|
|ScriptType|字符串|是|保留的属性，用于指示此源文件的类型。 可接受的值为 "JobConfig" （对于 JobConfig）。|
|Tags|JSON 键值对|否|标记是名称/值对，通过将相同的标记应用到多个资源和资源组，可以对资源进行分类并查看合并的帐单。 标记名称不区分大小写，标记值区分大小写。|

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-vs-code.md)
* [使用 Visual Studio Code 通过示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* 使用 Visual Studio Code
*[使用 CI/CD npm 包部署 Azure 流分析作业](setup-cicd-vs-code.md) [，以本地方式针对实时流输入测试流分析](visual-studio-code-local-run-live-input.md)