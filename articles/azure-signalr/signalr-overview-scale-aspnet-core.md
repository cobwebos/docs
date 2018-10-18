---
title: 使用 Azure SignalR 缩放 ASP.NET Core SignalR | Microsoft Docs
description: 使用 Azure SignalR Service 缩放 ASP.NET Core SignalR 应用程序的概述。
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: af100c015845d095f39ee4245f71689a4d2a43c0
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49384975"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>使用 Azure SignalR Service 缩放 ASP.NET Core SignalR 应用程序

## <a name="developing-signalr-apps"></a>开发 SignalR 应用

目前，可在 Web 应用程序中使用[两个版本](https://docs.microsoft.com/aspnet/core/signalr/version-differences) 的 SignalR：SignalR for ASP.NET 和 ASP.NET Core SignalR（此为最新版）。 Azure SignalR 服务是在 ASP.NET Core SignalR 上构建的 Azure 托管服务。 

ASP.NET Core SignalR 是以前版本的重写。 因此，ASP.NET Core SignalR 不与早期的 SignalR 版本后向兼容。 API 和行为不同。 ASP.NET Core SignalR SDK 面向 .NET Standard，因此仍可在 .NET Framework 中使用。 但是，必须改用新的 API（而不是旧的 API）。 如果正在使用 SignalR 并且想要迁移到 ASP.NET Core SignalR 或 Azure SignalR 服务，那么需要更改代码来处理 API 差异。

通过 Azure SignalR 服务，ASP.NET Core SignalR 的服务器端组件托管在 Azure 中。 但是，由于该技术构建在 ASP.NET Core 之上，因此可在多个平台（Windows、Linux 和 MacOS）上运行实际 Web 应用程序，同时将其托管在 [Azure 应用服务](../app-service/app-service-web-overview.md)、[IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index)、[Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx)、[Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) 和 [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) 中。 还可以在自己的进程中使用自托管。

如果应用程序的目标包括：支持最新功能，通过实时内容更新实现 Web 客户端更新；跨多个平台（Azure、Windows、Linux 和 macOS）运行；以及在不同环境中进行托管，那么最佳选项是利用 Azure SignalR Service。


## <a name="why-not-deploy-signalr-myself"></a>为什么不自行部署 SignalR？

将支持 ASP.NET Core SignalR 的 Azure Web 应用部署为整个 Web 应用程序的后端组件仍是一个有效的方法。

使用 Azure SignalR 服务的关键原因之一是其简便性。 借助 Azure SignalR 服务，无需处理性能、可伸缩性、可用性等问题。 已通过 99.9% 服务级别协议解决了这些问题。

此外，通常情况下，WebSocket 是支持实时内容更新的首选技术。 但是，缩放时，负载均衡大量持久性 WebSocket 连接是要解决的复杂问题。 常见解决方案通常利用：DNS 负载均衡、硬件负载均衡器和软件负载均衡。 Azure SignalR 服务为用户解决此问题。

使用它的另一原因可能是因为完全不需要实际托管一个 Web 应用程序。 Web 应用程序的逻辑可利用[无服务器计算](https://azure.microsoft.com/overview/serverless-computing/)。 例如，可能仅通过 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 触发器按需托管和执行代码。 这种情况可能会很棘手，因为代码仅按需运行，并且不会与客户端维持长久连接。 Azure SignalR 服务可以处理这种情况，因为该服务已为用户管理连接。 有关详细信息，请参阅[如何结合使用 SignalR Service 和 Azure Functions 的概述](signalr-overview-azure-functions.md)。 

## <a name="how-does-it-scale"></a>它如何缩放？

通常使用 SQL Server、Azure 服务总线或 Redis Cache 对 SignalR 进行缩放。 Azure SignalR 服务为用户处理缩放方法。 其性能和成本可与不具备处理这些其他服务的复杂性的方法相媲美。 用户只需更新服务的单元计数。 每个单元最多支持 1000 个客户端连接。

## <a name="next-steps"></a>后续步骤
* [快速入门：使用 Azure SignalR 创建聊天室](signalr-quickstart-dotnet-core.md)  
  

