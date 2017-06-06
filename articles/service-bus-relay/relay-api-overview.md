---
title: "Azure 中继 API 概述 | Microsoft Docs"
description: "可用的 Azure 中继 API 概述"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: edc730aa383b07dc308da8a160203faf3636208b
ms.contentlocale: zh-cn
ms.lasthandoff: 03/27/2017

---

# <a name="available-relay-apis"></a>可用的中继 API

## <a name="runtime-apis"></a>运行时 API

下面是当前可用的所有中继运行时客户端的列表。

有关每个运行时库状态的详细信息，请参阅[其他信息](#additional-information)。

| 语言/平台 | 可用功能 | 客户端程序包 | 存储库 |
| --- | --- | --- | --- |
| .NET Standard | 混合连接 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET framework | WCF 中继 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 不适用 |
| 节点 | 混合连接 | [hyco-ws](https://www.npmjs.com/package/hyco-ws)<br/>[hyco-websocket](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>其他信息

#### <a name="net"></a>.NET
.NET 生态系统具有多个运行时，因此事件中心有多个 .NET 库。 可以使用 .NET Core 或 .NET Framework 运行 .NET Standard 库，但 .NET Framework 库只能在 .NET Framework 环境中运行。 有关 .NET Frameworks 的详细信息，请参阅 [framework 版本](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请访问以下链接：
* [什么是 Azure 中继？](relay-what-is-it.md)
* [中继常见问题](relay-faq.md)
