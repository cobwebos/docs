---
title: 创建 Azure 库伯奈斯服务群集资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure 库伯奈斯服务 （AKS） 资源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877796"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>创建 Azure 库伯奈斯服务群集资源

1. 转到[Azure 库伯奈斯服务](https://ms.portal.azure.com/#create/microsoft.aks)，然后选择 **"创建**"。

1. 在 **"基础知识"** 选项卡上，输入以下信息：

    |设置|值|
    |--|--|
    |订阅|选择相应的订阅。|
    |资源组|选择可用资源组。|
    |Kubernetes 群集名称|输入名称（小写）。|
    |区域|选择附近的位置。|
    |Kubernetes 版本|任何值都标记为 **（默认）**。|
    |DNS 名称前缀|已自动创建，但您可以覆盖。|
    |节点大小|标准 DS2 v2：<br>`2 vCPUs`, `7 GB`|
    |节点计数|将滑块保留为默认值。|

1. 在 **"缩放"** 选项卡上，将**虚拟节点**和**VM 缩放集**设置为其默认值。
1. 在 **"身份验证**"选项卡上，将**服务主体**和**启用 RBAC**设置为其默认值。
1. 在“网络”选项卡上，输入以下选择：****

    |设置|值|
    |--|--|
    |HTTP 应用程序路由|否|
    |网络配置|Basic|

1. 在 **"监视"** 选项卡上，请确保**启用容器监视**设置为 **"是**"，并将**日志分析工作区**保留为默认值。
1. 在“标记”选项卡上，暂时让名称/值对保留空白****。
1. 选择 **"审阅并创建**"。
1. 通过验证后，选择“创建”。****

> [!NOTE]
> 如果验证失败，可能是因为"服务主体"错误。 返回 **"身份验证"** 选项卡，然后返回 **"审阅 + 创建**"，其中验证应运行，然后通过。
