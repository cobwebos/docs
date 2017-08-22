---
title: "Azure CLI 脚本示例 - 从群集中删除应用程序"
description: "Azure CLI 脚本示例 - 从 Service Fabric 群集中删除应用程序。"
services: service-fabric
documentationcenter: 
author: thraka
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
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>从 Service Fabric 群集中删除应用程序

此示例脚本会删除正在运行的 Service Fabric 应用程序实例，并从群集中注销应用程序类型和版本。  删除应用程序实例时也会删除与该应用程序关联的所有正在运行的服务实例。 接下来，会从映像存储区中删除应用程序文件。 

根据需要安装 [Azure CLI](../service-fabric-azure-cli-2-0.md)。

## <a name="sample-script"></a>示例脚本

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "从群集中删除应用程序")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 选择要使用的群集。 |
| [sf application delete](/cli/azure/sf/application#delete) | 从群集中删除应用程序实例。 |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | 从群集中注销 Service Fabric 应用程序类型和版本。|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | 从映像存储区中删除 Service Fabric 应用程序包。 |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure CLI 文档](../service-fabric-azure-cli-2-0.md)。

在 [Azure CLI 示例](../samples-cli.md)中可找到 Azure Service Fabric 的其他 Azure CLI 示例。

