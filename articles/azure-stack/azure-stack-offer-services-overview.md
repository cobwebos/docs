---
title: "提供 Azure 堆栈中的服务 |Microsoft 文档"
description: "作为云操作员，你可以向你的用户提供服务。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7e2b98391bab5806b59d8a16ec7dc24295a0d224
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="overview-of-offering-services-in-azure-stack"></a>提供 Azure 堆栈中的服务的概述

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

[Microsoft Azure 堆栈](azure-stack-poc.md)是混合云平台，可让你从数据中心提供服务。 作为服务提供商，你可以为你的租户提供服务。 在企业或政府机构中，你可以向员工提供本地服务。 你可以提供服务包括但不限于：

- 应用程序服务、 Mobile Apps、 API Apps、 API 函数、 SQL、 MySQL，如平台即服务 (PaaS) 服务。

你甚至可以合并服务集成并构建为不同的用户的复杂解决方案。

若要向你的用户提供这些服务，必须创建[计划、 服务和配额](azure-stack-plan-offer-quota-overview.md)。 然后可以将你的用户订阅为您提供要使用的服务。

## <a name="plan-your-service-offers"></a>计划您的服务提供

当你计划你的产品/服务时，请记住以下几点：

**试用版产品/服务**： 你可以使用试用版产品/服务以吸引新用户，然后才能升级到其他服务。 若要创建试用版产品/服务，创建一个较小[基本计划](azure-stack-plan-offer-quota-overview.md#base-plan)与可选的更大外接程序计划。

**容量规划**： 你可能会关心抓取大量的资源和拥塞系统为所有用户的用户。 若要帮助提高性能，您可以[配额配置你的计划](azure-stack-plan-offer-quota-overview.md#plans)cap 使用情况。

**委派提供程序**： 你可以授予其他人创建你的环境中提供的功能。 例如，如果你的服务提供商，你可以[委托](azure-stack-delegated-provider.md)到你的经销商此功能。 或者，如果你是组织，你可以委托到其他部门/子公司。

## <a name="next-steps"></a>接下来的步骤
[在 Azure 堆栈中创建提议](azure-stack-create-offer.md)

