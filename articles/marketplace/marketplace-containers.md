---
title: Azure Marketplace 中容器产品/服务的发布指南
description: 本文介绍了在 Azure Marketplace 中发布容器产品/服务的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/15/2020
ms.openlocfilehash: 81069b0340202a0a481522ef1920806026ebcbe1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119389"
---
# <a name="publishing-guide-for-container-offers"></a>容器产品/服务的发布指南

容器可帮助将容器映像发布到 Azure Marketplace。 使用本指南了解此产品/服务的要求。 

容器产品/服务是通过 Azure Marketplace 部署和计费的事务提供。 用户看到的号召性用语是“立即获取”。

如果解决方案是设置为基于 Kubernetes 的 Azure 容器服务实例的 Docker 容器映像，请使用*容器*产品/服务类型。 

> [!NOTE]
> 基于 Kubernetes 的 Azure 容器服务实例的示例包括： Azure Kubernetes 服务或 Azure 容器实例、为基于 Kubernetes 的容器运行时选择的 Azure 客户。  

Microsoft 目前支持免费和自带许可 (BYOL) 许可模型。

## <a name="container-offer-requirements"></a>容器套餐要求

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 支持免费或 BYOL 计费模型。<br><br> |  
| 从 Dockerfile 生成的映像 | 容器映像必须基于 Docker 映像规范并从 Dockerfile 生成。<br> <br>有关生成 Docker 映像的详细信息，请参阅[Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#usage)中的 "使用情况" 部分。<br><br> |  
| 在 Azure 容器注册表存储库中托管 | 容器映像必须承载于 Azure 容器注册表存储库中。<br> <br>有关使用 Azure 容器注册表的详细信息，请参阅[快速入门：使用 Azure 门户创建专用容器注册表](../container-registry/container-registry-get-started-portal.md)。<br><br> |  
| 映像标记 | 容器映像必须包含至少一个标记（标记的最大数目：16）。<br><br>有关标记映像的详细信息，请参阅 `docker tag` [Docker 文档](https://docs.docker.com/engine/reference/commandline/tag)站点上的页面。<br><br> |  

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[使用 Azure 市场发展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 有关详细信息，请参阅[创建 Azure 容器提供](./partner-center-portal/create-azure-container-offer.md)。
