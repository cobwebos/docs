---
title: "Azure CLI 脚本示例 - 将应用程序部署到群集"
description: "Azure CLI 脚本示例 - 将应用程序部署到 Service Fabric 群集。"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>将应用程序部署到 Service Fabric 群集

此示例脚本将应用程序包复制到群集映像存储，在群集中注册应用程序类型，并从应用程序类型创建应用程序实例。  如果目标应用程序类型的应用程序清单中定义了任何默认服务，则此时还会创建那些服务。

根据需要安装 [Azure CLI](../service-fabric-azure-cli-2-0.md)。

## <a name="sample-script"></a>示例脚本

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "将应用程序部署到群集")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用[删除应用程序](cli-remove-application.md)中的脚本删除应用程序实例，取消注册应用程序类型，并从映像存储中删除应用程序包。

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。


| 命令 | 说明 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 选择要使用的群集。 |
| [sf application upload](/cli/azure/sf/application#upload) | 上传应用文件和清单。 |
| [sf application provision](/cli/azure/sf/application#provision) | 在群集上注册应用程序。|
| [sf application create](/cli/azure/sf/application#create) | 创建应用程序实例并将所有已定义的服务部署到节点。 |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure CLI 文档](../service-fabric-azure-cli-2-0.md)。

在 [Azure CLI 示例](../samples-cli.md)中可找到 Azure Service Fabric 的其他 Azure CLI 示例。

