---
title: Azure CLI 示例 - 应用服务 | Microsoft 文档
description: Azure CLI 示例 - 应用服务
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: dc8a08c61250994b6083e3936820e1e6025593a6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066700"
---
# <a name="cli-samples-for-azure-app-service"></a>针对 Azure 应用服务的 CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [创建应用并使用 FTP 部署文件](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用并使用 FTP 将文件部署到该应用。 |
| [创建应用并从 GitHub 部署代码](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用并从公共 GitHub 存储库部署代码。 |
| [从 GitHub 使用连续部署创建应用](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用并从你拥有的 GitHub 存储库持续向其发布代码。 |
| [从本地 Git 存储库创建应用并部署代码](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用并配置从本地 Git 存储库进行的代码推送。 |
| [创建应用并将代码部署到过渡环境](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用并为其配置用于暂存代码更改的部署槽。 |
| [在 Docker 容器中创建 ASP.NET Core 应用](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| 在 Linux 上创建应用服务应用并从 Docker 中心加载 Docker 映像。 |
|**配置应用**||
| [将自定义域映射到应用](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用并将自定义域名映射到它。 |
| [将自定义 SSL 证书绑定到应用](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用并将自定义域名的 SSL 证书绑定到它。 |
|**缩放应用**||
| [手动缩放应用](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球应用](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 在两个不同地理区域中创建两个应用服务应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将应用连接到 SQL 数据库](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用和 SQL 数据库，然后将数据库连接字符串添加到应用设置。 |
| [将应用连接到存储帐户](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| 创建应用服务应用和存储帐户，然后将存储连接字符串添加到应用设置。 |
| [将应用连接到用于 Redis 的 Azure 缓存](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用和用于 Redis 的 Azure 缓存，然后将 Redis 连接详细信息添加到应用设置。） |
| [将应用连接到 Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用和 Cosmos DB，然后将 Cosmos DB 连接详细信息添加到应用设置。 |
|**备份和还原应用**||
| [备份应用](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用，并为其创建一次性备份。 |
| [为应用创建计划备份](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用，并为其创建计划的备份。 |
| [从备份中还原应用](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | 从备份中还原应用服务应用。 |
|**监视应用**||
| [使用 Web 服务器日志监视应用](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | 创建应用服务应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |