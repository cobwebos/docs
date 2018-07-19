---
title: Azure Service Fabric CLI (sfctl) 脚本删除示例
description: 使用 Azure Service Fabric CLI 从 Azure Service Fabric 群集中删除应用程序
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 24b645552212c502c06cc4df725e77c1d017340e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069305"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>从 Service Fabric 群集中删除应用程序

此示例脚本删除了正在运行的 Service Fabric 应用程序实例，并从群集中取消注册了应用程序类型和版本。  删除应用程序实例时也会删除与该应用程序关联的所有正在运行的服务实例。 接下来，会从映像存储区中删除应用程序文件。 

如果需要，请安装 [Service Fabric CLI](../service-fabric-cli.md)。

## <a name="sample-script"></a>示例脚本

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Service Fabric CLI 文档](../service-fabric-cli.md)。

在 [Service Fabric CLI 示例](../samples-cli.md)中可找到 Azure Service Fabric 的其他 Service Fabric CLI 示例。
