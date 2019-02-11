---
title: 在 Azure Stack 中提供服务 | Microsoft Docs
description: 作为云操作员，可以向用户提供服务。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 20cddc74dd05e982cbd332a65f9a477559caece7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244090"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>在 Azure Stack 中提供服务概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[Microsoft Azure Stack](azure-stack-poc.md)是一种混合云平台，让你从数据中心提供服务。 作为服务提供商，可以向租户提供服务。 在企业或政府机构中，你可以向员工提供本地服务。 

你可以提供[基础结构即服务](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) 服务，使用户能够构建按需计算基础结构，并通过 Azure Stack 用户门户进行预配和管理。

你还可以通过 Microsoft 和其他第三方提供商为 Azure Stack 部署[平台即服务](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 服务。 可以提供的服务包括但不限于：

- [将应用服务资源提供程序添加到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [将 SQL Server 资源提供程序添加到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [将 MySQL Server 资源提供程序添加到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


你甚至可以合并服务为不同用户集成和构建复杂解决方案。

若要向用户提供这些服务，必须创建[计划、套餐和配额](azure-stack-plan-offer-quota-overview.md)。 然后用户可以订阅套餐以使用这些服务。

## <a name="plan-your-service-offers"></a>计划服务套餐

计划套餐时，请记住以下几点：

**试用产品/服务**:可以使用试用版产品/服务吸引新用户，这些用户可以再升级到其他服务。 若要创建试用版套餐，请创建一个较小的[基本计划](azure-stack-plan-offer-quota-overview.md#base-plan)，其中包含一个可选的更大加载项计划。

**容量规划**:你可能会担心用户占用大量资源，阻塞所有用户的系统。 若要帮助提高性能，可以[配置带有配额的计划](azure-stack-plan-offer-quota-overview.md#plans)以限定使用量上限。

**委托的提供商**:您可以授予其他人创建的功能提供了您的环境中。 例如，如果你是服务提供商，可以将此功能[委托](azure-stack-delegated-provider.md)给经销商。 或者，如果你是组织，则可以委托给其他部门/子公司。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)
