---
title: 在 Azure API 管理中预配自承载网关 | Microsoft Docs
description: 了解如何在 Azure API 管理中预配自承载网关。
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
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075288"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中预配自承载网关

在 Azure API 管理实例中预配网关资源是部署自承载网关的先决条件。 本文详细介绍如何通过相关步骤在 API 管理中预配网关资源。

> [!NOTE]
> 自承载网关功能目前以预览版提供。 在预览期间，自承载网关仅在“开发人员”层和“高级”层提供，不额外收费。 “开发人员”层仅限单个自承载网关部署。

## <a name="prerequisites"></a>必备条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>预配自托管网关

1. 从“设置”下选择“网关”。  
2. 单击“+ 添加”。 
3. 输入网关的“名称”和“区域”。  
> [!TIP]
> “区域”指定将要与此网关资源关联的网关节点的预期位置。  它在语义上等效于一个与任何 Azure 资源关联的类似属性，但我们可以为其分配任意字符串值。

4. （可选）输入网关资源的“说明”  。
5. （可选）在“API”下选择“ **”+** ，  将一个或多个 API 与此网关资源相关联。
> [!TIP]
> 可以在某个 API 的“设置”选项卡中关联该 API，以及将其从网关中删除。 

> [!IMPORTANT]
> 默认情况下，现有 API 中的任何一个都不会与新网关资源相关联。 因此，尝试通过新网关调用它们会导致“`404 Resource Not Found`”响应。

6. 单击“添加”  。

现在已在 API 管理实例中预配网关资源， 可以部署网关了。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 详细了解如何[将自承载网关部署到 Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* 详细了解如何[将自承载网关部署到 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
