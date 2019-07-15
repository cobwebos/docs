---
title: 创建 Azure Kubernetes 服务群集资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure Kubernetes 服务 (AKS) 资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877464"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>创建 Azure Kubernetes 服务群集资源

1. 转到[Azure Kubernetes 服务](https://ms.portal.azure.com/#create/microsoft.aks)，然后选择**创建**。

1. 上**基础知识**选项卡上，输入以下信息：

    |设置|值|
    |--|--|
    |订阅|选择相应的订阅|
    |资源组|选择可用的资源组|
    |Kubernetes 群集名称|输入名称 （小写）|
    |区域|选择附近的位置|
    |Kubernetes 版本|1.12.8 （默认值）|
    |DNS 名称前缀|自动创建，但是您可以重写|
    |节点大小|标准 DS2 v2:<br>`2 vCPUs`， `7 GB`|
    |节点计数|将滑块保留为默认值|

1. 上**规模**选项卡上，保留**虚拟节点**并**虚拟机规模集 （预览版）** 设置为默认值。
1. 上**身份验证**选项卡上，保留**服务主体**并**启用 RBAC**设置为默认值。
1. 上**网络**选项卡上，输入以下选择：

    |设置|值|
    |--|--|
    |HTTP 应用程序路由|否|
    |网络配置|基本|

1. 上**监视**选项卡上，请确保**启用容器监视**设置为**是**，并将**Log Analytics 工作区**为默认值。
1. 上**标记**选项卡上，现在将名称/值对留空。
1. 选择**查看和创建**。
1. 验证通过后，选择**创建**。

> [!NOTE]
> 如果验证失败，可能会由于"服务主体"错误。 返回到**身份验证**选项卡，然后返回**查看 + 创建**，其中验证应运行，然后将传递。
