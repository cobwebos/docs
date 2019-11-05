---
title: 在 Azure API 管理中预配自我托管的网关 |Microsoft Docs
description: 了解如何在 Azure API 管理中预配自我托管的网关。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: ee9594e7aa3b55f7eb6fc7ba41783708555bd15e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513765"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中预配自我托管的网关

在 Azure API 管理实例中预配网关资源是部署自承载网关的先决条件。 本文逐步讲解如何在 API 管理中预配网关资源。

> [!NOTE]
> 自承载网关功能处于预览阶段。 在预览期间，仅在开发人员和高级层提供自承载的网关，无额外费用。 开发人员层限制为单个自行托管的网关部署。

## <a name="prerequisites"></a>必备组件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>预配自我托管的网关

1. 选择 "**设置**" 下的**网关**。
2. 单击“+ 添加”。
3. 输入网关的**名称**和**区域**。
> [!TIP]
> **区域**指定将与此网关资源关联的网关节点的预期位置。 它在语义上等效于与任何 Azure 资源关联的类似属性，但可以为其分配任意字符串值。
4. （可选）输入网关资源的**说明**。
5. （可选）在 " **api** " 下选择要将一个或多个 api 与此网关资源关联的 **+** 。
> [!TIP]
> 可以在 API 的 "**设置**" 选项卡上关联和删除网关中的 api。

> [!IMPORTANT]
> 默认情况下，将不会有任何现有 Api 与新的网关资源关联。 因此，尝试通过新的网关调用它们会导致 `404 Resource Not Found` 响应。
6. 单击“添加”。

现在，已在 API 管理实例中预配网关资源。 你可以继续部署网关。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅[AZURE API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 了解有关如何将[自承载网关部署到 Kubernetes 的](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)详细信息
* 了解有关如何将[自承载网关部署到 Docker 的](api-management-howto-deploy-self-hosted-gateway-to-docker.md)详细信息
