---
title: Azure 和 AppSource 市场套餐 | Microsoft Docs
description: 创建和管理 Azure 与 AppSource 市场的套餐
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f537a43f5d4d0431e1659daa258e0c1453f2295b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565865"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure 和 AppSource 市场套餐

本文的第一个部分介绍用于创建和管理适用于 Azure 和 AppSource 市场的套餐的常规操作。  此部分提供管理特定套餐类型所要了解的背景知识，以及所有套餐类型通用的技术信息。  本文的大部分内容包含了有关如何创建和管理特定套餐类型的详细说明。  

以下视频介绍了 Azure 市场或 AppSource 中提供的各种功能和不同套餐类型。  此外，其中还介绍了在这些市场中发布应用程序或服务的重要技术与业务要求。

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**生成适用于 Azure 市场和 AppSource 的应用与服务 - Build 2018**

有关这些市场的详细信息，请参阅 [Azure 市场和 AppSource 发布指南](../marketplace-publishers-guide.md)。


## <a name="common-offer-operations"></a>常见的产品/服务操作

创建新产品/服务的过程在不同的产品/服务类型之间有很大的不同，例如，在 [Azure 应用产品/服务](./azure-applications/cpp-azure-app-offer.md)与[咨询服务产品/服务](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)之间。  相比之下，你在[云合作伙伴门户](https://cloudpartner.azure.com)中对产品/服务执行的许多其他操作对不同的产品/服务类型都是相当标准化的。  [管理产品/服务](./manage-offers/cpp-manage-offers.md)部分中涵盖了这些常见操作，包括发布，查看状态，更新，以及删除。


## <a name="test-drive"></a>体验版

*体验版*是一项市场功能，它针对启用了此功能的每个产品/服务为客户提供了一个“先试后买”演示选项。  体验版功能仅限于以下部分产品/服务类型：[Azure 应用程序](./azure-applications/cpp-azure-app-offer.md)、[Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md)、[Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md)、[Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md)、[SaaS 应用程序](./saas-app/cpp-saas-offer.md)和[虚拟机](./virtual-machine/cpp-virtual-machine-offer.md)。  此功能需要发布者创建为其产品/服务自定义的体验版模板。  有关详细信息，请参阅[体验版](./test-drive/what-is-test-drive.md)部分。

可以通过应用[体验版筛选器](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive)来浏览具有体验版演示的现有市场产品/服务。 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure 市场和 AppSource 套餐类型

下表列出了[云合作伙伴门户](https://cloudpartner.azure.com)当前支持的套餐类型。  对于每个套餐类型，其中指出了套餐在市场中的列出位置，以及套餐解决方案技术的一般说明。

|                产品/服务类型                |  市场  |   描述                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure 应用程序](./azure-applications/cpp-azure-app-offer.md) | Azure | 解决方案包括通过 Azure 资源管理器模板部署的一个或多个虚拟机 (VM) 以及可选的自定义 Azure 代码。  部署可以由客户通过解决方案模板来完成，或者由发布者管理。 这种类型提供的灵活性比所提供的虚拟机套餐类型更高。  |
| [咨询服务](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Azure 和 AppSource | Microsoft 认可的顾问可以在 Azure 市场或 AppSource 中列出其域特定的服务。  他们的专业知识可帮助客户评估问题，以及根据业务目标创建和部署适当的解决方案。  |
| [容器](./containers/cpp-containers-offer.md)  | Azure | 解决方案是预配为基于 Kubernetes 的服务或 Azure 容器实例的 Docker 容器映像。 |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | 一个可以扩展此企业资源规划 (ERP) 和业务管理系统的包。 |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | 扩展了此客户的包资源管理 (CRM) 系统，通过其销售、 服务、 项目服务和域服务模块  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | 扩展此企业资源规划 (ERP) 服务，该支持高级财务、 操作、 制造和供应链管理包 |
| [IoT Edge 模块](./iot-edge-module/cpp-offer-process-parts.md) | Azure | 一个可在 IoT Edge 设备上运行的 Docker 兼容容器。  其中包含小型计算模块，这些模块使用自定义代码、其他 Azure 服务和第三方服务的组合。 |
| [Power BI 应用](./power-bi/cpp-power-bi-offer.md) | AppSource | Power BI 应用包所包括的数据集、 报表和仪表板的可自定义 Power BI 内容 |
| [SaaS 应用](./saas-app/cpp-saas-offer.md) | Azure | 解决方案是发布服务器中，哪些用户通过使用 Azure Active Directory 的自定义界面登录管理的软件即服务订阅。 |
| [虚拟机](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | 解决方案包含在部署到客户订阅的单个虚拟机中。  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

有关详细信息，请参阅[按套餐类型提供的发布指南](../publisher-guide-by-offer-type.md)。


## <a name="next-steps"></a>后续步骤

您将了解可以在 marketplace 产品/服务及其常见技术特性和一文中的资产执行的常规操作[管理产品](./manage-offers/cpp-manage-offers.md)。
