---
title: Azure RBAC 的最佳实践
description: 使用 Azure 基于角色的访问控制（Azure RBAC）的最佳做法。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735328"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC 的最佳实践

本文介绍使用 Azure 基于角色的访问控制（Azure RBAC）的一些最佳实践。 这些最佳实践源于我们的 Azure RBAC 经验和客户体验。

## <a name="only-grant-the-access-users-need"></a>仅授予用户所需的访问权限

使用 Azure RBAC，可以在团队中实现职责分离，并且可以仅向用户授予执行作业所需的访问权限。 无需向每个人授予 Azure 订阅或资源的无限制权限，可以仅允许在特定的范围执行某些操作。

规划访问控制策略时，最佳做法是授予用户完成工作所需的最低权限。 下图显示了使用 Azure RBAC 的建议模式。

![Azure RBAC 和最低权限](./media/best-practices/rbac-least-privilege.png)

有关如何添加角色分配的信息，请参阅[使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)。

## <a name="limit-the-number-of-subscription-owners"></a>限制订阅所有者的数量

你最多应具有3个订阅所有者，以减少受到泄露的所有者的潜在侵害。 可以在 Azure 安全中心监视此建议。 有关安全中心中的其他标识和访问建议，请参阅[安全建议-参考指南](../security-center/recommendations-reference.md)。

## <a name="use-azure-ad-privileged-identity-management"></a>使用 Azure AD Privileged Identity Management

为了保护特权帐户免受恶意网络攻击，可以使用 Azure Active Directory Privileged Identity Management (PIM) 来降低权限的暴露时间，并通过报表和警报增加对使用的可见性。 PIM 提供对 Azure AD 和 Azure 资源的实时特权访问，从而帮助保护特权帐户。 在自动撤消权限后，访问可以进行时间绑定。 

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。

## <a name="next-steps"></a>后续步骤

- [排查 Azure RBAC 问题](troubleshooting.md)