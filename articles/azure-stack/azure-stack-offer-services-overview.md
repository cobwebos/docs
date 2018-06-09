---
title: 在 Azure Stack 中提供服务 | Microsoft Docs
description: 作为云操作员，可以向用户提供服务。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 042e65cfe350cb61124ed8920ae3616502e6553d
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248837"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>在 Azure Stack 中提供服务概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[Microsoft Azure 堆栈](azure-stack-poc.md)是混合云平台，可让你从数据中心提供服务。 作为服务提供商，可以向租户提供服务。 在企业或政府机构中，你可以向员工提供本地服务。 你可以提供服务包括但不限于：

- 如平台即服务 (PaaS) 服务的应用程序服务、 API Apps、 API 函数、 SQL 和 MySQL。

你甚至可以合并服务为不同用户集成和构建复杂解决方案。

若要向用户提供这些服务，必须创建[计划、产品/服务和配额](azure-stack-plan-offer-quota-overview.md)。 然后用户可以订阅产品/服务以使用这些服务。

## <a name="plan-your-service-offers"></a>计划服务产品

计划产品/服务时，请记住以下几点：

**试用版产品/服务**：可以使用试用版产品/服务吸引新用户，然后这些用户可以再升级到其他服务。 若要创建试用版产品/服务，请创建一个较小的[基本计划](azure-stack-plan-offer-quota-overview.md#base-plan)，其中包含一个可选的更大加载项计划。

**容量规划**：你可能会担心用户占用大量资源，阻塞所有用户使用的系统。 若要帮助提高性能，可以[配置带有配额的计划](azure-stack-plan-offer-quota-overview.md#plans)以限定使用量上限。

**授权供应商**：可以授权其他人在你的环境中创建产品/服务。 例如，如果你是服务提供商，可以将此功能[委托](azure-stack-delegated-provider.md)给经销商。 或者，如果你是组织，则可以委托给其他部门/子公司。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 中创建产品/服务](azure-stack-create-offer.md)
