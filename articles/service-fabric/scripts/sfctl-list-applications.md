---
title: "Service Fabric CLI 脚本示例 - 列出群集上的应用程序"
description: "Service Fabric CLI 脚本示例 - 列出 Service Fabric 群集上预配的应用程序。"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: adegeo
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b99c54e85f22c5745a852e10bb66368da3b5d8f9
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>将应用程序证书添加到 Service Fabric 群集

此脚本示例将连接到 Service Fabric 群集并列出所有预配的应用程序。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>示例脚本

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Service Fabric CLI 文档](../service-fabric-cli.md)。

在 [Service Fabric CLI 示例](../samples-cli.md)中可找到 Azure Service Fabric 的其他 Service Fabric CLI 示例。

