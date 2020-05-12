---
title: 在订阅者与 CSP 之间转移 Azure 订阅
description: 了解如何在订阅者与 CSP 之间转移 Azure 订阅。
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: banders
ms.openlocfilehash: b198a781da6e18c363e90d8bd5314dafc563dcca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791147"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>在订阅者与 CSP 之间转移 Azure 订阅

本文提供了用于在云解决方案提供商 (CSP) 合作伙伴与其客户之间转移 Azure 订阅的概要步骤。

## <a name="transfer-ea-subscriptions"></a>转移 EA 订阅

认证为 [Azure 专家托管服务提供商 (MSP)](https://partner.microsoft.com/membership/azure-expert-msp)的 CSP 直接帐单合作伙伴可以请求为其具有直接企业协议 (EA) 的客户转移 Azure 订阅。 仅允许为已接受了 Microsoft 客户协议 (MCA) 并购买了某个 Azure 计划的客户进行订阅转移。

当请求获得批准后，CSP 可以向其客户提供组合发票。 若要详细了解 CSP 转移订阅，请参阅[获取你的 MPA 帐户的 Azure 订阅的计费所有权](mpa-request-ownership.md)。

## <a name="other-subscription-transfers-to-a-csp-partner"></a>将其他订阅转移到 CSP 合作伙伴

若要将任何其他 Azure 订阅转移到 CSP 合作伙伴，订阅者需要将资源从源订阅转移到 CSP 订阅。 请使用以下指南在订阅之间移动资源。

1. 与你的 CSP 合作伙伴合作来创建目标 Azure CSP 订阅。
1. 请确保源订阅和目标 CSP 订阅位于相同的 Azure Active Directory (Azure AD) 租户中。  
    你不能更改 Azure CSP 订阅的 Azure AD 租户。 相反，你必须将源订阅添加或关联到 CSP Azure AD 租户。 有关详细信息，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。
    > [!IMPORTANT]
    > - 将订阅关联到其他 Azure AD 目录时，如果用户的角色是使用[基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 分配的，则用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
    > - 将订阅与其他目录相关联时，还会从订阅中删除策略分配。
1. 用来执行转移的用户帐户必须对这两个订阅都具有 [RBAC](add-change-subscription-administrator.md) 所有者访问权限。
1. 在开始之前，[验证](/rest/api/resources/resources/validatemoveresources)是否所有 Azure 资源都可以从源订阅移动到目标订阅。  
    某些 Azure 资源无法在订阅之间移动。 若要查看可移动的 Azure 资源的完整列表，请参阅[资源对移动操作的支持](../../azure-resource-manager/management/move-support-resources.md)。
    > [!IMPORTANT]
    >  - Azure CSP 仅支持 Azure 资源管理器资源。 如果源订阅中的 Azure 资源是使用 Azure 经典部署模型创建的，则在迁移之前必须先将其迁移到 [Azure 资源管理器](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm)。 你必须是合作伙伴才能查看此网页。

1. 验证是否所有源订阅服务都使用 Azure 资源管理器模型。 然后，使用 [Azure 资源移动](../../azure-resource-manager/management/move-resource-group-and-subscription.md)将资源从源订阅转移到目标订阅。
    > [!IMPORTANT]
    >  - 在订阅之间移动 Azure 资源可能会导致服务停机，这取决于订阅中的资源。

## <a name="all-subscription-transfers-from-a-csp-partner"></a>从 CSP 合作伙伴转移所有订阅

若要将任何其他订阅从 CSP 合作伙伴转移到任何其他 Azure 套餐，订阅者需要在源 CSP 订阅与目标订阅之间移动资源。

1. 创建目标 Azure 订阅。
1. 请确保源订阅和目标订阅位于相同的 Azure Active Directory (Azure AD) 租户中。 有关更改 Azure AD 租户的详细信息，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。
    请注意，此更改目录不是云解决方案提供商订阅。 例如，你要从云解决方案提供商订阅转移到即用即付订阅。 你需要更改即用即付订阅的目录，使之与该目录匹配。

    > [!IMPORTANT]
    >  - 将订阅关联到其他目录时，如果用户的角色是使用[RBAC](../../role-based-access-control/role-assignments-portal.md) 分配的，则用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
    >  - 将订阅与其他目录相关联时，还会从订阅中删除策略分配。

1. 用来执行转移的用户帐户必须对这两个订阅都具有 [RBAC](add-change-subscription-administrator.md) 所有者访问权限。
1. 在开始之前，[验证](/rest/api/resources/resources/validatemoveresources)是否所有 Azure 资源都可以从源订阅移动到目标订阅。
    > [!IMPORTANT]
    >  - 某些 Azure 资源无法在订阅之间移动。 若要查看可移动的 Azure 资源的完整列表，请参阅[资源对移动操作的支持](../../azure-resource-manager/management/move-support-resources.md)。

1. 使用 [Azure 资源移动](../../azure-resource-manager/management/move-resource-group-and-subscription.md)将资源从源订阅转移到目标订阅。
    > [!IMPORTANT]
    >  - 在订阅之间移动 Azure 资源可能会导致服务停机，这取决于订阅中的资源。

## <a name="next-steps"></a>后续步骤
- [获取 MPA 帐户的 Azure 订阅的计费所有权](mpa-request-ownership.md)。
- 了解如何[使用 Azure 计费管理帐户和订阅](index.yml)。
