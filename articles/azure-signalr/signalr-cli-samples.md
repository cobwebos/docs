---
title: Azure CLI 示例 - Azure SignalR 服务 | Microsoft Docs
description: Azure CLI 示例 - Azure SignalR 服务
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779848"
---
# <a name="azure-cli-samples"></a>Azure CLI 示例

下表包含使用 Azure CLI 生成的 bash 脚本的链接，该脚本适用于 Azure SignalR 服务。

| | |
|-|-|
|**创建**||
| [创建新的 SignalR 服务和资源组](scripts/signalr-cli-create-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。  |
|**集成**||
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用](scripts/signalr-cli-create-with-app-service.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 此外，还会添加新的 Web 应用和应用服务计划，以托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过“应用设置”进行配置，以便连接到新 SignalR 服务资源。 |
| [创建配置为使用 SignalR 的新的 SignalR 服务和 Web 应用，以及 GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 在新资源组中新建一个以随机产生的名称命名的 Azure SignalR 服务资源。 此外，还会添加新的 Azure Web 应用和托管计划，以托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过“应用设置”进行配置，用于新 SignalR 服务资源和客户端密码的连接字符串，以支持 [GitHub 身份验证](https://developer.github.com/v3/guides/basics-of-authentication/)，如[身份验证教程](signalr-authenticate-oauth.md)所示。 Web 应用还配置为使用本地 Git 存储库部署资源。 |
| | |
