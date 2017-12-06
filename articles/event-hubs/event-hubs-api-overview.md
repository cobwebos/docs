---
title: "Azure 事件中心 API 概述 | Microsoft 文档"
description: "可用的 Azure 事件中心 API 概述"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="available-event-hubs-apis"></a>可用的事件中心 API

本文介绍一组可用于管理事件中心资源的 API 客户端。

## <a name="runtime-apis"></a>运行时 API

下表介绍了当前可用的所有 Azure 事件中心运行时客户端。 虽然其中某些库也包含有限的管理功能，但也有专用于管理操作的[特定库](#management-apis)。 这些库的核心功能是通过事件中心发送和接收消息。

有关每个运行时库的当前状态的详细信息，请参阅[其他信息](#additional-information)。

| 语言/平台 | 客户端程序包 | EventProcessorHost 包 | 存储库 |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | 不适用 |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| 节点 | [NPM](https://www.npmjs.com/package/azure-event-hubs) | 不适用 | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | 不适用 | 不适用 | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>其他信息

#### <a name="net"></a>.NET
.NET 生态系统具有多个运行时，因此事件中心有多个 .NET 库。 可以使用 .NET Core 或 .NET Framework 运行 .NET Standard 库，但 .NET Framework 库只能在 .NET Framework 环境中运行。 有关 .NET Frameworks 的详细信息，请参阅 [framework 版本](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)。

#### <a name="node"></a>节点

Node.js 库目前处于预览状态，由 Microsoft 员工和外部参与者作为副项目进行维护。 包括源代码在内的所有贡献都欢迎并将对其进行审查。

## <a name="management-apis"></a>管理 API

下面是当前可用的所有特定于管理的库的列表。 这些库并非仅包含运行时操作，并且管理事件中心实体不是其唯一目的。

| 语言/平台 | 管理包 | 存储库 |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)