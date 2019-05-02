---
title: Azure 容器映像产品/服务 |Azure Marketplace
description: 概述了在 Azure 市场中发布容器产品/服务的过程。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pabutler
ms.openlocfilehash: 9617380822f5814d1cbd2bd2b69eac72722fe813
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942753"
---
# <a name="containers"></a>容器

<table> <tr> <td>本部分介绍了如何将容器映像发布到 <a href="https://azuremarketplace.microsoft.com">Azure 市场</a>。  
容器产品/服务类型支持预配为 <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes 服务</a>实例或 <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure 容器实例</a>并托管在 <a href="https://docs.microsoft.com/azure/container-registry">Azure 容器注册表</a>存储库中的 Docker 容器映像。 </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>产品/服务组件

本部分概述了发布容器的要素，为在 Azure 市场中进行发布的发布者提供指导。 发布过程划分为以下主要组成部分：

- [先决条件](./cpp-prerequisites.md) - 列出创建或发布容器产品/服务之前所要满足的技术和业务要求。
- [创建产品/服务](./cpp-create-offer.md) - 列出使用云合作伙伴门户创建新的容器产品/服务项需要执行的步骤。
- [准备技术资产](./cpp-create-technical-assets.md) - 如何将容器解决方案的技术资产创建为 Azure 市场上的产品/服务。
- [发布产品/服务](./cpp-publish-offer.md) - 如何提交产品/服务，以便发布到 Azure 市场。

## <a name="container-publishing-process"></a>容器发布过程

下图演示了发布 VM 套餐中的概要步骤。
![发布产品/服务的步骤](./media/containers-offer-process.png)

概括而言，发布容器产品/服务的步骤如下：

1. 创建产品/服务 - 提供有关产品/服务的详细信息。 此信息包括：产品/服务说明、营销材料、支持信息和资产规范。
2. 创建业务和技术资产 - 为关联的解决方案（托管在 Azure 容器注册表中的容器映像）创建业务资产（法律文档和营销材料）和技术资产。
3. 创建 SKU - 创建与产品/服务关联的 SKU。 必须为要发布的每个映像指定唯一的 SKU。
4. 认证并发布产品/服务 - 填写产品/服务和技术资产后，可以提交该产品/服务。 提交后会启动发布过程。 在此过程中，将会测试、验证并认证该解决方案，然后在 Azure 市场中将其“推出”。

## <a name="next-steps"></a>后续步骤

在执行这些步骤之前，必须满足有关将容器发布到 Microsoft Azure 市场的[技术和业务要求](./cpp-prerequisites.md)。