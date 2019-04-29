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
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749765"
---
# <a name="azure-application-offer"></a>Azure 应用程序产品/服务

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> 本部分介绍了如何将新的 Azure 应用程序产品/服务发布到 [Azure 市场](https://azuremarketplace.microsoft.com)。  每个 Azure 应用程序都包含一个 Azure 资源管理器模板，该模板定义应用程序使用的所有技术资产，其中通常包括一个或多个虚拟机以及基于 Azure 或基于 Web 的其他支持服务。 所有 Azure 应用产品/服务都必须通过 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) 启用访问安全性。  </div> | ![Azure 应用图标](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>发布概述

下面的视频[为 Azure 市场构建解决方案模板和托管实例](https://channel9.msdn.com/Events/Build/2018/BRK3603)是一个简介：哪些产品/服务类型可用；哪些技术资产是必需的；如何创作 Azure 资源管理器模板；开发和测试应用 UI；如何发布应用产品/服务；以及应用评审过程。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Azure 应用程序类型

有两种类型的 Azure 应用程序：托管应用程序和解决方案模板。 

- 解决方案模板是在市场中发布解决方案的主要方式之一。 如果解决方案需要在单个虚拟机 (VM) 以外实现附加的部署和配置自动化，则使用此产品/服务类型。 使用解决方案模板可自动提供多个 VM。 此自动化包括预配网络和存储资源以提供复杂的 IaaS 解决方案。 有关解决方案模板要求和计费模型的概述，请参阅 [Azure 应用程序：解决方案模板](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)。

- 托管应用程序类似于解决方案模板，但有一个重大差异。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用 Azure 托管应用程序轻松生成完全托管的统包应用程序并交付给客户。

除了 Azure 市场之外，还可在服务目录中提供托管应用程序。 服务目录是为组织中的用户提供的已批准解决方案内部目录。 可使用该目录来确保满足组织标准，同时为组织中的团体提供解决方案。 员工可以使用目录轻松找到 IT 部门推荐和批准的应用程序。

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴通道参加现已推出。  请参阅[云解决方案提供商](../../cloud-solution-providers.md)营销通过 Microsoft CSP 产品/服务的详细信息的合作伙伴渠道。

有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](https://docs.microsoft.com/azure/managed-applications/overview)。


## <a name="publishing-process-workflow"></a>发布过程工作流

下图显示发布 Azure 应用程序产品/服务的概要过程。

![发布产品/服务的工作流](./media/new-offer-process.png)

概括而言，发布 Azure 应用程序产品/服务的步骤包括：

1. 满足[先决条件](./cpp-prerequisites.md)（未显示）- 验证是否已满足将 Azure 应用发布到 Azure 市场的业务和技术要求。 

1. [创建产品/服务](./cpp-create-offer.md) - 提供有关产品/服务的详细信息。 此信息包括：产品/服务说明、营销材料、支持信息和资产规范。

1. [创建或收集现有的业务和技术资产](./cpp-create-technical-assets.md) - 为关联的解决方案创建业务资产（法律文档和营销材料）及技术资产。

1. [创建 SKU](./cpp-skus-tab.md) - 创建与产品/服务关联的 SKU。 必须为要发布的每个映像指定唯一的 SKU。

1. 认证并[发布产品/服务](./cpp-publish-offer.md) - 填写产品/服务和技术资产后，可以提交该产品/服务。 提交后会启动发布过程。 在此过程中，将会测试、验证并认证该解决方案，然后在 Azure 市场中将其“推出”。

## <a name="next-steps"></a>后续步骤

在执行这些步骤之前，必须满足有关将托管应用程序发布到 Microsoft Azure 市场的[技术和业务要求](./cpp-prerequisites.md)。
