---
title: Azure 流分析作业分析.json 字段
description: 本文列出了 Azure 流分析作业 Config.json 文件的支持字段，用于在可视化工作室代码中创建作业。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617952"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 流分析作业分析.json 字段

用于[使用 Visual Studio 代码创建 Azure 流分析作业](quick-create-vs-code.md)的*JobConfig.json*文件中支持以下字段。

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

|“属性”|类型|必选|“值”|
|----|----|--------|-----|
|数据本地化|字符串|否|流分析作业的数据区域设置。 值应为受支持的名称。 如果未指定，则默认为"en-US"。|
|输出错误策略|字符串|否|指示要应用于到达输出的事件的策略，并且由于格式错误（缺少列值、类型或大小错误的列值）无法写入外部存储的事件。 - 停止或掉落|
|事件延迟到达最大延迟秒|integer|否|可以包括事件延迟的最大可容忍延迟（以秒为单位）。 支持的范围为 -1 到 1814399 （20.23：59：59 天）和 -1 用于无限期指定等待。 如果属性不存在，则将其解释为值为 -1。|
|事件出序最大延迟秒|integer|否|可调整订单外事件以按顺序返回的最大可容忍延迟（以秒为单位）。|
|事件订单外策略|字符串|否|指示要应用于输入事件流中顺序顺序耗尽的事件的策略。 - 调整或掉落|
|流式处理单元|integer|是|指定流式处理作业使用的流式处理单元数。|
|CompatibilityLevel|字符串|否|控制流式处理作业的某些运行时行为。 - 可接受的值为"1.0"、"1.1"、"1.2"|
|使用系统分配标识|boolean|否|设置为 true 以启用此作业使用托管 Azure 活动目录标识与其他 Azure 服务本身进行通信。|
|全局存储.帐户名称|字符串|否|全局存储帐户用于存储与流分析作业相关的内容，如 SQL 引用数据快照。|
|全局存储.帐户密钥|字符串|否|全局存储帐户的对应密钥。|
|数据来源凭据域|字符串|否|用于凭据本地存储的保留属性。|
|脚本类型|字符串|是|保留属性以指示此源文件的类型。 可接受值为 JobConfig.json 的"JobConfig"。。|
|Tags|JSON 键值对|否|标记是名称/值对，使您能够通过将同一标记应用于多个资源和资源组来对资源进行分类并查看合并计费。 标记名称不区分大小写，标记值区分大小写。|

## <a name="next-steps"></a>后续步骤

* [在可视化工作室代码中创建 Azure 流分析作业](quick-create-vs-code.md)
* [使用 Visual Studio Code 通过示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [通过使用可视化工作室代码](visual-studio-code-local-run-live-input.md)
*[使用 CI/CD npm 包部署 Azure 流分析作业](setup-cicd-vs-code.md)，在本地测试流分析查询与实时流输入