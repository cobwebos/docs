---
title: 创建 Azure Kubernetes Service 群集资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure Kubernetes 服务（AKS）资源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877796"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>创建 Azure Kubernetes Service 群集资源

1. 请参阅[Azure Kubernetes 服务](https://ms.portal.azure.com/#create/microsoft.aks)，并选择 "**创建**"。

1. 在 "**基本**信息" 选项卡上，输入以下信息：

    |设置|值|
    |--|--|
    |订阅|选择相应的订阅。|
    |资源组|选择可用的资源组。|
    |Kubernetes 群集名称|输入名称（小写）。|
    |区域|选择附近的位置。|
    |Kubernetes 版本|任何值标记为 **（默认值）**。|
    |DNS 名称前缀|自动创建，但你可以重写。|
    |节点大小|标准 DS2 v2：<br>`2 vCPUs`, `7 GB`|
    |节点计数|将滑块保持为默认值。|

1. 在 "**缩放**" 选项卡上，将**虚拟节点**和**VM 规模集**设置为其默认值。
1. 在 "**身份验证**" 选项卡上，将 "**服务主体**" 和 "**启用 RBAC** " 设置为默认值。
1. 在“网络”选项卡上，输入以下选择：****

    |设置|值|
    |--|--|
    |HTTP 应用程序路由|否|
    |网络配置|基本|

1. 在 "**监视**" 选项卡上，确保 "**启用容器监视**" 设置为 **"是"**，并将**Log Analytics 工作区**保留为默认值。
1. 在“标记”选项卡上，暂时让名称/值对保留空白****。
1. 选择 "**检查和创建**"。
1. 通过验证后，选择“创建”。****

> [!NOTE]
> 如果验证失败，则可能是由于 "服务主体" 错误导致的。 返回到 "**身份验证**" 选项卡，然后返回以**查看 + 创建**，验证应在此运行，然后通过。
