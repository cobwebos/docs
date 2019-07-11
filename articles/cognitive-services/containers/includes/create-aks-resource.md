---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建 azure kubernetes (AKS) 资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: eb2d609c3a7dfd769c6a19854366ac5e45fddb7d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712479"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>创建 Azure Kubernetes 服务 (AKS) 群集资源

1. 转到[创建](https://ms.portal.azure.com/#create/microsoft.aks)为 Kubernetes 服务。

1. 上**基础知识**选项卡上，输入以下详细信息：

    |设置|ReplTest1|
    |--|--|
    |订阅|选择相应的订阅|
    |资源组|选择可用的资源组|
    |Kubernetes 群集名称|所需的名称 （小写）|
    |区域|选择附近的位置|
    |Kubernetes 版本|1.12.8 （默认值）|
    |DNS 名称前缀|自动创建的但可以选择重写|
    |节点大小|标准 DS2 v2:<br>`2 vCPUs`， `7 GB`|
    |节点计数|将滑块保留为默认值|

1. 上**规模**选项卡上，保留*虚拟节点*并*虚拟机规模集*默认值。
1. 上**身份验证**选项卡上，保留*服务主体*并*启用 RBAC*默认值。
1. 上**网络**选项卡上，输入以下选择：

    |设置|值|
    |--|--|
    |HTTP 应用程序路由|否|
    |网络配置|基本|

1. 上**监视**选项卡上，确保*启用容器监视*设置为**是**并将*Log Analytics 工作区*为其默认值
1. 上**标记**选项卡上，现在将名称/值对留空。
1. 单击**查看和创建**。
1. 验证通过后，单击**创建**。

> [!NOTE]
> 如果验证失败，它可能是由于*服务主体*错误。 导航回到*身份验证*选项卡，然后回*查看 + 创建*应该执行，然后传递验证的位置。
