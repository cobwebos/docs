---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733269"
---
扩展捆绑包进行发布的 Azure Functions 团队可通过中的设置的所有绑定*host.json*文件。 进行本地开发，请确保具有最新版本[Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)。

若要使用扩展捆绑包，更新*host.json*文件以包括以下条目`extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id`属性引用的命名空间的 Microsoft Azure Functions 扩展捆绑包。
- `version`引用的包的版本。

作为捆绑包更改中的包捆绑在版本增量。 主版本的更改会发生仅移动时在绑定中的包的主版本。 `version`属性使用[间隔表示法，用于指定版本范围](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 Functions 运行时总是选择接口定义的版本范围或间隔的最高允许版本。

后在项目中引用的扩展捆绑包，然后所有默认绑定都可供你的函数。 中提供的绑定[扩展包](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)是：

|程序包  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|