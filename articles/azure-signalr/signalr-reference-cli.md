---
title: Azure CLI 示例 - Azure SignalR 服务
description: 按照真实示例了解 Azure SignalR 服务的 Azure CLI。 你将了解如何创建具有更多 Azure 服务的 SignalR 服务。
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158128"
---
# <a name="azure-cli-reference"></a>Azure CLI 参考

下表包含使用 Azure CLI 生成的 bash 脚本的链接，该脚本适用于 Azure SignalR 服务。

| | |
|-|-|
|**创建**||
| [创建新的 SignalR 服务和资源组](scripts/signalr-cli-create-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。  |
|**集成**||
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用](scripts/signalr-cli-create-with-app-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 还添加了一个新的 Web 应用和应用服务计划，用于托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过“应用设置”进行配置，以便连接到新 SignalR 服务资源。 |
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用，以及 GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 还添加了一个新的 Azure Web 应用和托管计划，用于托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过“应用设置”进行配置，用于新 SignalR 服务资源和客户端密码的连接字符串，以支持 [GitHub 身份验证](https://developer.github.com/v3/guides/basics-of-authentication/)，如[身份验证教程](signalr-concept-authenticate-oauth.md)所示。 Web 应用还配置为使用本地 Git 存储库部署资源。 |
| | |
