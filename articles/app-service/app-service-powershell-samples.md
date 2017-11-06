---
title: "Azure PowerShell 示例 - 应用服务 | Microsoft 文档"
description: "Azure PowerShell 示例 - 应用服务"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b587f299e7b11effd05cbbc4b15976fee3f83ffd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell 示例

下表包含指向使用 Azure PowerShell 生成的 bash 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [从 GitHub 使用部署创建 Web 应用](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建从 GitHub 拉取代码的 Azure Web 应用。 |
| [从 GitHub 使用连续部署创建 Web 应用](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建从 GitHub 持续部署代码的 Azure Web 应用。 |
| [使用 FTP 创建 Web 应用并部署代码](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 使用 FTP 从本地目录创建 Azure Web 应用并上传文件。 |
| [从本地 Git 存储库创建 Web 应用并部署代码](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 从本地 Git 存储库创建 Azure Web 应用并配置代码推送。 |
| [创建 Web 应用并将代码部署到过渡环境](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 使用用于暂存代码更改的部署槽创建 Azure Web 应用。 |
|**配置应用**||
| [将自定义域映射到 Web 应用](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Web 应用并将自定义域名映射到它。 |
| [将自定义 SSL 证书绑定到 Web 应用](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Web 应用并将自定义域名的 SSL 证书绑定到它。 |
|**缩放应用**||
| [手动缩放 Web 应用](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建 Azure Web 应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球 Web 应用](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在两个不同地理区域中创建两个 Azure Web 应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将 Web 应用连接到 SQL 数据库](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Web 应用和 SQL 数据库，然后将数据库连接字符串添加到应用设置。 |
| [将 Web 应用连接到存储帐户](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Web 应用和存储帐户，然后将存储连接字符串添加到应用设置。 |
|**备份和还原应用**||
| [备份 Web 应用](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建 Azure Web 应用，并为其创建一次性备份。 |
| [为 Web 应用创建计划备份](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建 Azure Web 应用，并为其创建计划备份。 |
| [删除 Web 应用的备份](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 删除 Web 应用的现有备份。 |
|**监视应用**||
| [使用 Web 服务器日志监视 Web 应用](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建 Azure Web 应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |
