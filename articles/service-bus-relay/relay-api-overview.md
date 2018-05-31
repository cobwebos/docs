---
title: Azure 中继 API 概述 | Microsoft Docs
description: 可用的 Azure 中继 API 概述
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 00496ca6c0138a840322c053d7d20944df228e9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893436"
---
# <a name="available-relay-apis"></a>可用的中继 API

## <a name="runtime-apis"></a>运行时 API

下表列出了当前可用的所有中继运行时客户端。

[其他信息](#additional-information)部分包含有关每个运行时库状态的详细信息。

| 语言/平台 | 可用功能 | 客户端程序包 | 存储库 |
| --- | --- | --- | --- |
| .NET Standard | 混合连接 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET framework | WCF 中继 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 不适用 |
| 节点 | 混合连接 | [Websocket：`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websocket：`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP 请求：`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>其他信息

#### <a name="net"></a>.NET

.NET 生态系统具有多个运行时，因此中继有多个 .NET 库。 可以使用 .NET Core 或 .NET Framework 运行 .NET Standard 库，但 .NET Framework 库只能在 .NET Framework 环境中运行。 有关 .NET Frameworks 的详细信息，请参阅 [framework 版本](/dotnet/articles/standard/frameworks#framework-versions)。

.NET Framework 库仅支持 WCF 编程模型并依赖于基于 WCF `net.tcp` 传输的专有二进制协议。 保留此协议和库是为了实现与现有应用程序的后向兼容性。

.NET Standard 库基于混合连接中继的开放协议定义，该中继构建在 HTTP 和 WebSocket 上。 该库支持通过 Websocket 进行流抽象和用于答复 HTTP 请求的简单请求-响应 API 手势。 [Web API](https://github.com/Azure/azure-relay-dotnet) 示例演示如何集成混合连接和适用于 Web 服务的 ASP.NET Core。

#### <a name="nodejs"></a>Node.js

上表列出的混合连接模块将现有 Node.js 模块替换或修改为侦听 Azure 中继服务而不是本地网络堆栈的备用实现。

`hyco-https` 模块修改并部分替代核心 Node.js 模块 `http` 和 `https`，提供能与许多现有 Node.js 模块和依赖于这些核心模块的应用程序兼容的 HTTPS 侦听器实现。

`hyco-ws` 和 `hyco-websocket` 模块修改 Node.js 的常用 `ws` 和 `websocket` 模块，提供备用侦听器实现，该实现使得模块和依赖于其中一个模块的应用程序呢能够在混合连接中继的后台工作。

有关这些模块的详细信息可在 [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub 存储库中找到。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 中继的详细信息，请访问以下链接：
* [什么是 Azure 中继？](relay-what-is-it.md)
* [中继常见问题](relay-faq.md)