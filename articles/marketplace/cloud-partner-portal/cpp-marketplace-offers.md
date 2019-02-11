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
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 0d6879c6b9be06af4bb289761cc8f26b7e56d18e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355032"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure 和 AppSource 市场套餐

本文的第一个部分介绍用于创建和管理适用于 Azure 和 AppSource 市场的套餐的常规操作。  此部分提供管理特定套餐类型所要了解的背景知识，以及所有套餐类型通用的技术信息。  本文的大部分内容包含了有关如何创建和管理特定套餐类型的详细说明。  

以下视频介绍了 Azure 市场或 AppSource 中提供的各种功能和不同套餐类型。  此外，其中还介绍了在这些市场中发布应用程序或服务的重要技术与业务要求。

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**生成适用于 Azure 市场和 AppSource 的应用与服务 - Build 2018**

有关这些市场的详细信息，请参阅 [Azure 市场和 AppSource 发布指南](../marketplace-publishers-guide.md)。


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure 市场和 AppSource 套餐类型

下表列出了[云合作伙伴门户](https://cloudpartner.azure.com)当前支持的套餐类型。  对于每个套餐类型，其中指出了套餐在市场中的列出位置，以及套餐解决方案技术的一般说明。

|                产品/服务类型                |  市场  |   Description                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure 应用程序](./azure-applications/cpp-azure-app-offer.md) | Azure | 解决方案包括通过 Azure 资源管理器模板部署的一个或多个虚拟机 (VM) 以及可选的自定义 Azure 代码。  部署可以由客户通过解决方案模板来完成，或者由发布者管理。 这种类型提供的灵活性比所提供的虚拟机套餐类型更高。  |
| [咨询服务](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Azure 和 AppSource | Microsoft 认可的顾问可以在 Azure 市场或 AppSource 中列出其域特定的服务。  他们的专业知识可帮助客户评估问题，以及根据业务目标创建和部署适当的解决方案。  |
| [容器](./containers/cpp-containers-offer.md)  | Azure | 解决方案是预配为基于 Kubernetes 的服务或 Azure 容器实例的 Docker 容器映像。 |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | 一个可以扩展此企业资源规划 (ERP) 和业务管理系统的包。 |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | 一个可以通过其销售、服务、项目服务和现场服务模块扩展此客户资源管理 (CRM) 系统的包。  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | 一个可以扩展此企业资源规划 (ERP) 服务并支持高级财务、运营、制造和供应链管理的包。 |
| [IoT Edge 模块](./iot-edge-module/cpp-offer-process-parts.md) | Azure | 一个可在 IoT Edge 设备上运行的 Docker 兼容容器。  其中包含小型计算模块，这些模块使用自定义代码、其他 Azure 服务和第三方服务的组合。 |
| [SaaS 应用](./saas-app/cpp-saas-offer.md) | Azure | 解决方案是发布者管理的软件即服务订阅，用户可以通过利用 Azure Active Directory 的自定义界面登录其中。 |
| [虚拟机](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | 解决方案包含在部署到客户订阅的单个虚拟机中。  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

有关详细信息，请参阅[按套餐类型提供的发布指南](../publisher-guide-by-offer-type.md)。


## <a name="next-steps"></a>后续步骤

在[管理套餐](./manage-offers/cpp-manage-offers.md)主题中了解可对市场套餐执行的常规操作，以及套餐的常用技术属性和资产。
