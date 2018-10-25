---
title: IoT Edge 模块市场常见问题解答 | Microsoft Docs
description: IoT Edge 模块市场常见问题解答。
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805548"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>IoT Edge 模块市场常见问题解答


## <a name="what-is-the-edge-module-marketplace"></a>Edge 模块市场是什么？


IoT Edge 模块市场是经认证的预生成 Edge 模块的列表，这些模块可以通过 Azure 市场发现。

![IoT Edge 模块](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Edge 模块在何处可见？ 


在 [Azure 门户市场](https://ms.portal.azure.com/)（经身份验证的体验）的“物联网”类别下，标记为“IoT Edge 模块”。

在 [Azure 市场网站](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1)（匿名体验）的“物联网”类别下，标记为“IoT Edge 模块”。

## <a name="is-it-open-to-partners"></a>它是否对合作伙伴开放？


还不可以。 目前，仅 Microsoft 授权的模块发布在市场的 IoT Edge 部分。 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>为何合作伙伴应发布其 IoT Edge 模块？


-   通过将此渠道添加到市场并展示其解决方案，增加其产品的可见性。

-   争取更多潜在顾客。 在 Azure 市场中，他们可以详细了解谁对其解决方案感兴趣。

-   抢先利用更多的货币化功能。

## <a name="what-is-the-onboarding-process"></a>上架过程是怎样的？


上架过程将通过 Azure 市场来完成，但目前尚未开放。 详细指南在 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)中。 请参阅容器的事务处理商品类型。 

合作伙伴先需要成为 Azure 市场的发布者， 然后才能通过[云合作伙伴门户](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md)将其 Edge 模块上架。

## <a name="are-there-any-monetization-capabilities"></a>是否有任何货币化功能？


目前没有现成的货币化功能。 我们的第一个目标是开一个提供“免费”或“自带许可证”Edge 模块的市场。 我们会增加更多的货币化功能，例如用量计费模型。

## <a name="what-is-bring-your-own-license-byol"></a>什么是自带许可 (BYOL)？


[Microsoft Azure 市场参与政策](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)中的正式定义是：

- 客户在 Azure 市场之外获取访问或使用产品/服务的权利，且不会因为在 Azure 市场中使用该产品/服务而需要支付 Azure 市场费用。

是否对软件实施许可并挣取收入取决于合作伙伴。

## <a name="can-partners-publish-a-freemium-module"></a>合作伙伴是否可以发布“免费增值”模块？


可以。免费增值模块是可以免费使用但带有一些限制的模块，会被视为其他类型的发布。

## <a name="is-intellectual-property-protected"></a>知识产权是否受保护？


Edge 模块是兼容 Docker 的容器。 保护包装在分发的容器中的知识产权 (IP) 由合作伙伴自行负责。

## <a name="where-will-the-modules-be-hosted"></a>模块在何处托管？


IoT Edge 模块将托管在 Microsoft 拥有的可以匿名查询的容器注册表中，例如 Docker 中心。

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>在 Azure 市场和 Azure IoT 工具之间进行集成的计划有哪些？

我们将在 Azure 市场和 Azure IoT 工具之间构建更紧密的集成。 例如，我们打算启用一些浏览功能，允许直接从 IoT 中心门户或 Visual Studio Code 浏览 IoT Edge 模块市场。

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>我的容器应该支持哪个操作系统或体系结构？

IoT Edge 模块与生产环境中的 IoT Edge 必须支持相同的 OS/体系结构矩阵。 该列表保留在 [Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中。 例如，模块当前必须支持 Linux x64 和 Linux ARM32。

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>是否有其他需要了解的认证约束？

我们仍在制定具体的认证约束集。 我们的指导原则是：

-   质胜于量。

-   特定于 IoT Edge 的容器（而不是随意的容器）。

-   生产级。

-   一键式部署（至少提供一组默认配置值）。

## <a name="any-other-questions"></a>其他问题？


请联系 [Azure IoT Edge 上架部门](mailto:azureiotedgeonboarding@service.microsoft.com)。
