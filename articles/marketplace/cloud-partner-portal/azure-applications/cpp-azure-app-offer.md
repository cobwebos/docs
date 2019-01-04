---
title: Azure 应用程序产品/服务 | Microsoft Docs
description: 概述在 Azure 市场中发布 Azure 应用程序产品/服务的过程。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195371"
---
# <a name="azure-application-offer"></a>Azure 应用程序产品/服务

本部分介绍如何将新的 Azure 应用程序产品/服务发布到 <a href="https://azuremarketplace.microsoft.com">Azure 市场</a>。
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 每个 Azure 应用程序都包含一个 Azure 资源管理器模板，该模板定义应用程序使用的所有技术资产，其中通常包括一个或多个虚拟机以及基于 Azure 或基于 Web 的其他支持服务。 | ![Azure 应用图标](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>优点

在 Microsoft 市场中列出你的应用程序的一些好处包括：

* 在 Office 365 和 Dynamics 365 中接触 1 亿 Azure Active Directory 用户。

* 扩展你的销售团队：接触世界各地的业务用户并获得能够吸引最终用户的销售渠道，帮助开发潜在顾客，并与各个行业的新客户进行聊天。

* 获取可操作的见解：我们将就你的应用在 AppSource 上的表现、有效的方法以及如何进一步改进销售过程共享见解。

## <a name="types-of-azure-applications"></a>Azure 应用程序类型

有两种类型的 Azure 应用程序：托管应用程序和解决方案模板。 尽管它们十分相似，但仍有一些明显的区别。

### <a name="solution-template"></a>解决方案模板

解决方案模板是在市场中发布解决方案的主要方式之一。 如果解决方案需要在单个虚拟机 (VM) 以外实现附加的部署和配置自动化，则使用此产品/服务类型。 使用解决方案模板可自动提供多个 VM。 这包括预配网络和存储资源以提供复杂的 IaaS 解决方案。 有关解决方案模板要求和计费模型的概述，请参阅 [Azure 应用程序：解决方案模板](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)。

### <a name="managed-application"></a>托管应用程序

托管应用程序类似于市场中的解决方案模板，但有一个重大差异。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用 Azure 托管应用程序轻松生成完全托管的统包应用程序并交付给客户。

除了市场，还可在服务目录中提供托管应用程序。 服务目录是为组织中的用户提供的已批准解决方案内部目录。 可使用该目录来确保满足组织标准，同时为组织中的团体提供解决方案。 员工可以使用目录轻松找到 IT 部门推荐和批准的应用程序。

有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](https://docs.microsoft.com/azure/managed-applications/overview)。

## <a name="publishing-overview"></a>发布概述

以下视频（[针对 Azure 市场构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)）首先概述了如何创建 Azure 资源管理器模板来定义 Azure 应用程序解决方案，然后概述了之后如何将应用产品/服务发布到 Azure 市场。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>发布过程工作流

下图显示发布 Azure 应用程序产品/服务的概要过程。

![发布产品/服务的工作流](./media/new-offer-process.png)

## <a name="offer-components"></a>产品/服务组件

本部分概述发布托管应用程序产品/服务的要素，为在 Azure 市场中进行发布的发布者提供指导。 发布过程划分为以下主要组成部分： 

* [先决条件](./cpp-prerequisites.md)：列出创建或发布托管应用程序产品/服务之前所要满足的技术和业务要求。 
* [创建产品/服务](./cpp-create-offer.md)：列出使用云合作伙伴门户创建托管应用程序产品/服务项需要执行的步骤。 
* [发布产品/服务](./cpp-publish-offer.md)：介绍如何提交产品/服务，以便发布到 Azure 市场。

## <a name="steps-in-the-publishing-process"></a>发布过程步骤

概括而言，发布 Azure 应用程序产品/服务的步骤包括：

1. 创建产品/服务 - 提供有关产品/服务的详细信息。 此信息包括：产品/服务说明、营销材料、支持信息和资产规范。

2. 创建业务和技术资产 - 为关联的解决方案创建业务资产（法律文档和营销材料）及技术资产。

3. 创建 SKU - 创建与产品/服务关联的 SKU。 必须为要发布的每个映像指定唯一的 SKU。

4. 认证并发布产品/服务 - 填写产品/服务和技术资产后，可以提交该产品/服务。 提交后会启动发布过程。 在此过程中，将会测试、验证并认证该解决方案，然后在 Azure 市场中将其“推出”。

## <a name="next-steps"></a>后续步骤

在执行这些步骤之前，必须满足有关将托管应用程序发布到 Microsoft Azure 市场的[技术和业务要求](./cpp-prerequisites.md)。