---
title: Azure CLI 示例 - Azure SignalR 服务
description: Azure CLI 示例 - Azure SignalR 服务
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071070"
---
# <a name="azure-cli-reference"></a>Azure CLI 参考

下表包含使用 Azure CLI 生成的 bash 脚本的链接，该脚本适用于 Azure SignalR 服务。

| | |
|-|-|
|**创建**||
| [创建新的 SignalR 服务和资源组](scripts/signalr-cli-create-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。  |
|**集成**||
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用](scripts/signalr-cli-create-with-app-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 此外将添加新的 Web 应用和应用服务计划来托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过“应用设置”进行配置，以便连接到新 SignalR 服务资源。 |
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用，以及 GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 此外将添加新的 Azure Web 应用和托管主机使用 SignalR 服务的 ASP.NET Core Web 应用的计划。 Web 应用通过“应用设置”进行配置，用于新 SignalR 服务资源和客户端密码的连接字符串，以支持 [GitHub 身份验证](https://developer.github.com/v3/guides/basics-of-authentication/)，如[身份验证教程](signalr-concept-authenticate-oauth.md)所示。 Web 应用还配置为使用本地 Git 存储库部署资源。 |
| | |
