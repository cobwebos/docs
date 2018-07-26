---
title: Azure 市场的“容器”产品/服务发布指南
description: 本文介绍在市场中发布容器的要求
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 5eb30c65032332825d05097f86d0275b015a8929
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057579"
---
# <a name="containers-offer-publishing-guide"></a>“容器”产品/服务发布指南

“容器”产品/服务可帮助你将容器映像发布到 Azure 市场。 使用本指南了解此产品/服务的要求。 

以下是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。

如果解决方案是预配为基于 Kubernetes 的 Azure 容器服务的 Docker 容器映像，请使用“容器”产品/服务类型。

>[!NOTE]
>例如，基于 Kubernetes 的容器运行时的 Azure 客户可以选择 Azure Kubernetes 服务或 Azure 容器实例等基于 Kubernetes 的 Azure 容器服务。  

Microsoft 目前支持免费和自带许可 (BYOL) 许可模型。

## <a name="containers-offer"></a>“容器”产品/服务

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 支持免费或 BYOL 计费模型。 |  
| 从 Dockerfile 生成的映像 | 容器映像必须基于 Docker 映像规范，并且必须从 Dockerfile 生成。<ul> <li>有关构建 docker 镜像的详细信息，请访问位于 [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage) 内的“使用情况”部分。</li> </ul> |  
| 在 ACR 中托管 | 容器映像必须在 Azure 容器注册表 (ACR) 存储库中托管。<ul> <li>有关使用 ACR 的详细信息，请访问位于 [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) 内的快速入门：使用 Azure 门户页面创建容器注册表。</li> </ul> |  
| 映像标记 | 容器映像必须包含至少 1 个标记（最多标记数：16）。<ul> <li>有关标记图像的详细信息，请访问位于 [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag) 内的 docker 标记页。</li> </ul> |  


## <a name="next-steps"></a>后续步骤

如果尚未注册， 

- 请在市场中[注册](https://azuremarketplace.microsoft.com/sell)

如果已注册并正在创建新套餐或正在使用现有套餐，

- [登录到云合作伙伴门户](https://cloudpartner.azure.com)创建或完成套餐
