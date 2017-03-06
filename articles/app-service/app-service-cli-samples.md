---
title: "Azure CLI 示例 - 应用服务 | Microsoft 文档"
description: "Azure CLI 示例 - 应用服务"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: f2c95ec5cd32632bb5b9683f5273721f465aebdf
ms.openlocfilehash: 2d7d2154a2910c0bd7ff24c404c4eb05203b1453
ms.lasthandoff: 03/01/2017


---
# <a name="azure-app-service-cli-samples"></a>Azure 应用服务 CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [从 GitHub 使用部署创建 Web 应用](./scripts/app-service-cli-deploy-github.md)| 创建从 GitHub 拉取代码的 Azure Web 应用。 |
| [从 GitHub 使用连续部署创建 Web 应用](./scripts/app-service-cli-continuous-deployment-github.md)| 创建从 GitHub 连续部署代码的 Azure Web 应用。 |
| [从 Visual Studio Team Services 使用连续部署创建 Web 应用](./scripts/app-service-cli-continuous-deployment-vsts.md)| 创建从 Visual Studio Team Services 连续部署代码的 Azure Web 应用。 |
| [从本地 Git 存储库创建 Web 应用并部署代码](./scripts/app-service-cli-deploy-local-git.md) | 从本地 Git 存储库创建 Azure Web 应用并配置代码推送。 |
| [创建 Web 应用并将代码部署到过渡环境](./scripts/app-service-cli-deploy-staging-environment.md) | 使用用于暂存代码更改的部署槽创建 Azure Web 应用。 |
| [在 Docker 容器中从 Docker Hub 创建 ASP.NET Core Web 应用](./scripts/app-service-cli-linux-docker-aspnetcore.md)| 在 Linux 上创建 Azure Web 应用并从 Docker Hub 加载 Docker 映像。 |
| [在 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core Web 应用](./scripts/app-service-cli-linux-acr-aspnetcore.md)| 在 Linux 上创建 Azure Web 应用并从 Azure 容器注册表加载 Docker 映像。 |
|**配置应用**||
| [将自定义域映射到 Web 应用](./scripts/app-service-cli-configure-custom-domain.md)| 创建 Azure Web 应用并将自定义域名映射到它。 |
|**缩放应用**||
| [手动缩放 Web 应用](./scripts/app-service-cli-scale-manual.md) | 创建 Azure Web 应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球 Web 应用](./scripts/app-service-cli-scale-high-availability.md) | 在两个不同地理区域中创建两个 Azure Web 应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将 Web 应用连接到 SQL 数据库](./scripts/app-service-cli-app-service-sql.md)| 创建 Azure Web 应用和 SQL 数据库，然后将数据库连接字符串添加到应用设置。 |
| [将 Web 应用连接到存储帐户](./scripts/app-service-cli-app-service-storage.md)| 创建 Azure Web 应用和存储帐户，然后将存储连接字符串添加到应用设置。 |
|**监视应用**||
| [使用 Web 服务器日志监视 Web 应用](./scripts/app-service-cli-monitor.md) | 创建 Azure Web 应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |

