---
title: "Azure CLI 脚本示例 - 在 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 在 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core Web 应用"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/23/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 862aad510a9d6e8ae15324457d8b15378b2b2776
ms.openlocfilehash: 61d0f71d13429cffa3b7e01cf2fb18eb358a1817
ms.lasthandoff: 02/27/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>在 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core Web 应用

在此方案中，你将学习如何创建资源组、Linux 应用服务计划和 Web 应用，并使用 Docker 容器通过 Azure 容器注册表部署 ASP.NET Core 应用程序。

在运行此脚本前，请确保已使用 `az login` 命令创建与 Azure 的连接。

## <a name="create-app-sample"></a>创建应用示例

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux Azure 容器注册表")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、VM 以及所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | 创建应用服务计划中的 Azure Web 应用。 |
| [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | 为 Azure Web 应用设置 Docker 容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
