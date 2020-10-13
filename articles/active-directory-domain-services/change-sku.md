---
title: 更改 Azure AD 域服务的 SKU | Microsoft Docs
description: 了解如何在业务需求发生变化的情况下更改 Azure AD 域服务托管域的 SKU 层
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: c85a3f3262da6002e9604b1506552b9188a64460
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962745"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>更改现有 Azure Active Directory 域服务托管域的 SKU

在 Azure Active Directory 域服务 (Azure AD DS) 中，可用的性能和功能基于 SKU 类型。 这些功能差异包括备份频率或单向出站林信任的最大数量。

创建托管域时，你可以选择一个 SKU，并且可以在部署托管域后，根据业务需求的变化上下切换 SKU。 业务需求的变化可能包括需要更频繁地备份或创建其他林信任。 若要详细了解不同 SKU 的限制和定价，请参阅 [Azure AD DS SKU 概念][concepts-sku]和 [Azure AD DS 定价][pricing]页。

本文介绍如何使用 Azure 门户更改现有 Azure AD DS 托管域的 SKU。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置托管域][create-azure-ad-ds-instance]的教程。

## <a name="sku-change-limitations"></a>SKU 更改限制

部署托管域后，可以向上或向下更改 SKU。 但是，如果使用资源林并且已创建从 Azure AD DS 到本地 AD DS 环境的单向出站林信任，则 SKU 更改操作会有一些限制。 高级和企业 SKU 定义可创建的信任数量限制 。 无法将其更改为最大限制低于当前配置的 SKU。

例如：

* 如果已在高级 SKU 上创建了两个林信任，则无法将其向下更改为标准 SKU 。 标准 SKU 不支持林信任。
* 或者，如果已在高级 SKU 上创建了七个林信任，则无法将其向下更改为企业 SKU 。 企业 SKU 最多支持五个信任关系。

若要详细了解这些限制，请参阅 [Azure AD DS SKU 功能和限制][concepts-sku]。

## <a name="select-a-new-sku"></a>选择新的 SKU

若要使用 Azure 门户更改托管域的 SKU，请完成以下步骤：

1. 在 Azure 门户的顶部，搜索并选择“Azure AD 域服务”。 从列表选择托管域，例如 aaddscontoso.com。
1. 在 Azure AD DS 页左侧的菜单中，选择“设置”>“SKU”。

    ![在 Azure 门户中为 Azure AD DS 托管域选择 SKU 菜单选项](media/change-sku/overview-change-sku.png)

1. 从下拉菜单中，选择希望用于托管域的 SKU。 如果有资源林，则不能选择标准 SKU，因为林信任仅在企业 SKU 或更高版本上可用 。

    从下拉菜单中选择所需的 SKU，然后选择“保存”。

    ![从 Azure 门户的下拉菜单中选择所需的 SKU](media/change-sku/change-sku-selection.png)

更改 SKU 类型可能需要一两分钟的时间。

## <a name="next-steps"></a>后续步骤

如果有资源林，并且想在 SKU 更改后创建其他信任，请参阅[在 Azure AD DS 中创建到本地域的出站林信任][create-trust]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
