---
title: "Azure Service Fabric CLI 脚本部署示例"
description: "使用 Azure Service Fabric CLI 将应用程序部署到 Azure Service Fabric 群集"
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
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 74d54b314e81dc260e800e32d6c3edd5215c82b4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>将应用程序部署到 Service Fabric 群集

此示例脚本将应用程序包复制到群集映像存储，在群集中注册应用程序类型，并从应用程序类型创建应用程序实例。 此时还将创建任何默认服务。

如果需要，请安装 [Service Fabric CLI](../service-fabric-cli.md)。

## <a name="sample-script"></a>示例脚本

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "将应用程序部署到群集")]

## <a name="clean-up-deployment"></a>清理部署

完成后，可使用[删除](cli-remove-application.md)脚本删除应用程序。 删除脚本会删除应用程序实例、注销应用程序类型，并从映像存储中删除应用程序包。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Service Fabric CLI 文档](../service-fabric-cli.md)。

在 [Service Fabric CLI 示例](../samples-cli.md)中可找到 Azure Service Fabric 的其他 Service Fabric CLI 示例。

