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
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5d74f8216edd46fcd36d2b7f8763ec8a69ccad42
ms.lasthandoff: 03/31/2017


---
# <a name="azure-cli-samples"></a>Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [从 GitHub 创建 Web 应用并部署代码](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| 从公共 GitHub 存储库创建 Azure Web 应用并部署代码。 |
| [从 GitHub 使用连续部署创建 Web 应用](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 从你拥有的 GitHub 存储库通过连续发布创建 Azure Web 应用。 |
| [从本地 Git 存储库创建 Web 应用并部署代码](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | 从本地 Git 存储库创建 Azure Web 应用并配置代码推送。 |
| [创建 Web 应用并将代码部署到过渡环境](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 使用用于暂存代码更改的部署槽创建 Azure Web 应用。 |
| [在 Docker 容器中创建 ASP.NET Core Web 应用](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| 在 Linux 上创建 Azure Web 应用并从 Docker Hub 加载 Docker 映像。 |
|**配置应用**||
| [将自定义域映射到 Web 应用](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| 创建 Azure Web 应用并将自定义域名映射到它。 |
| [将自定义 SSL 证书绑定到 Web 应用](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| 创建 Azure Web 应用并将自定义域名的 SSL 证书绑定到它。 |
|**缩放应用**||
| [手动缩放 Web 应用](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | 创建 Azure Web 应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球 Web 应用](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 在两个不同地理区域中创建两个 Azure Web 应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将 Web 应用连接到 SQL 数据库](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| 创建 Azure Web 应用和 SQL 数据库，然后将数据库连接字符串添加到应用设置。 |
| [将 Web 应用连接到存储帐户](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| 创建 Azure Web 应用和存储帐户，然后将存储连接字符串添加到应用设置。 |
| [将 Web 应用连接到 Redis 缓存](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | 创建 Azure Web 应用和 Redis 缓存，然后将 Redis 连接详细信息添加到应用设置。 |
| [将 Web 应用连接到 documentdb](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | 创建 Azure Web 应用和 documentdb，然后将 documentdb 连接详细信息添加到应用设置。 |
|**监视应用**||
| [使用 Web 服务器日志监视 Web 应用](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | 创建 Azure Web 应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |
