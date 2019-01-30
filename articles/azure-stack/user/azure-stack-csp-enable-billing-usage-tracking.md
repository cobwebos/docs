---
title: 允许云服务提供商管理 Azure Stack 订阅 | Microsoft Docs
description: 允许云服务提供商访问 Azure Stack 中的订阅。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 5d7398853e20702aef450e2532784f0dca7aac57
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246596"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果使用 Azure Stack 与云服务提供商 (CSP)，则可以选择来管理访问的资源在 Azure 和 Azure Stack 订阅。 还可以让提供商管理你的订阅。 本文介绍以下操作：

* 让服务提供商访问你的订阅。
* 确保服务提供商可以管理你的服务。

> [!NOTE]
> 如果 CSP 不管理你的帐户，并且你跳过以下步骤，CSP 不能管理你的 Azure Stack 订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有用户角色的来宾用户添加到你的租户目录。 若要将用户添加的步骤，请参阅[向 Azure Active Directory 添加新用户](../../active-directory/add-users-azure-active-directory.md)
2. CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到你的租户目录。 若要将用户添加的步骤，请参阅[向 Azure Active Directory 添加新用户](../../active-directory/add-users-azure-active-directory.md)
2. 添加**所有者**到 CSP 来宾用户的角色。 有关将 CSP 用户添加到你的订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问](../../role-based-access-control/role-assignments-portal.md)。 CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../azure-stack-billing-and-chargeback.md)。
