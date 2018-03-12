---
title: "启用云服务提供程序，用于管理 Azure 堆栈订阅 |Microsoft 文档"
description: "启用要访问 Azure 堆栈中的订阅的服务提供程序。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 4bc5644425aa11fb210d81095e4166baefc6432e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>启用云服务提供程序，用于管理 Azure 堆栈订阅

*适用于：Azure Stack 集成系统*

如果你使用的 Azure 堆栈与云服务提供程序 (CSP)，提供程序可能会管理你的访问的资源在你的 Azure 订阅和 Azure 堆栈中。 或者，你可能会管理你自己的订阅。 本文探讨如何可以启用服务提供商来访问你代表你的订阅，或若要确保服务提供商可以管理你的服务。

> [!Note]  
>  如果将跳过以下步骤，和 CSP 已不管理你的帐户，然后 CSP 将不能用于管理 Azure 堆栈订阅替你。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>管理你的订阅与云服务提供商

1. 将你的 CSP 以与用户角色的来宾用户身份添加到你的租户目录。  有关将用户添加的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 然后将创建你的本地 Azure 堆栈订阅。
3. 你就可以开始使用 Azure 堆栈。
3. 你的 CSP 然后应以验证它们还可以管理你的资源在订阅中创建资源。 例如，他们能够[使用 Azure 堆栈门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>启用云服务提供商来管理你的订阅使用 RBAC 权限

将 CSP 作为所有者添加到你的订阅。 

1. 将你的 CSP 添加为 guest 用户。 与对你的租户目录的所有者角色中。  有关将用户添加的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 将所有者角色添加到 CSP guest 用户。 有关将 CSP 用户添加到你的订阅的步骤，请参阅[Use Role-Based 访问控制来管理对你的 Azure 订阅的资源的访问](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)
3. CSP 然后将创建你的本地 Azure 堆栈订阅。
4. 你就可以开始使用 Azure 堆栈。
5. 你的 CSP 然后应以验证他们可以管理你的资源在订阅中创建资源。 

## <a name="next-steps"></a>后续步骤

  - 若要了解有关如何从 Azure 堆栈中检索资源使用情况信息的详细信息，请参阅[使用情况和 Azure 堆栈中的计费](../azure-stack-billing-and-chargeback.md)。
