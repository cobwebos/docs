---
title: 更改 Azure AD 域服务的 SKU |Microsoft Docs
description: 如果你的业务要求发生更改，了解 Azure AD 域服务托管域的 SKU 层
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: b65310569e95173b88dd0aa0dfe1dbacd86cc8fc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126706"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>更改现有 Azure AD 域服务托管域的 SKU

在 Azure Active Directory 域服务（Azure AD DS）中，可用的性能和功能基于 SKU 类型。 这些功能差异包括备份频率或单向出站林信任的最大数量（当前为预览版）。 当你创建托管域时，你可以选择 SKU，你可以在部署托管域后，随着业务需求的变化向上或向下切换 Sku。 业务需求的变化可能包括需要更频繁的备份或创建更多的林信任。 有关不同 Sku 的限制和定价的详细信息，请参阅[AZURE AD DS SKU 的概念][concepts-sku]和[Azure AD ds 定价][pricing]页。

本文说明如何使用 Azure 门户更改现有 Azure AD DS 托管域的 SKU。

## <a name="before-you-begin"></a>开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。

## <a name="sku-change-limitations"></a>SKU 更改限制

部署 Azure AD DS 托管域后，可以向上或向下更改 Sku。 但是，如果使用资源林（当前为预览版），并已创建从 Azure AD DS 到本地 AD DS 环境的单向出站林信任，则 SKU 更改操作有一些限制。 *高级版*和*企业版*sku 定义了可以创建的信任数量限制。 你无法更改到最大限制低于当前配置的 SKU。

例如：

* 如果在*高级*sku 上创建了两个林信任，则无法更改为*标准*sku。 *标准*SKU 不支持林信任。
* 或者，如果你在*高级*SKU 上创建了7个信任，则无法更改为*企业*sku。 *企业*SKU 最多支持五个信任关系。

有关这些限制的详细信息，请参阅[AZURE AD DS SKU 功能和限制][concepts-sku]。

## <a name="select-a-new-sku"></a>选择新的 SKU

若要使用 Azure 门户更改 Azure AD DS 托管域的 SKU，请完成以下步骤：

1. 在 Azure 门户顶部，搜索并选择**Azure AD 域服务**"。 从列表中选择托管域，如*aaddscontoso.com*。
1. 在 Azure AD DS "页左侧的菜单中，选择"**设置 "> SKU**"。

    ![在 Azure 门户中选择 Azure AD DS 托管域的 SKU 菜单选项](media/change-sku/overview-change-sku.png)

1. 从下拉菜单中，选择要用于 Azure AD DS 托管域的 SKU。 如果有资源林，则不能选择*标准*SKU，因为林信任仅适用于*企业*SKU 或更高版本。

    从下拉菜单中选择所需的 SKU，然后选择 "**保存**"。

    ![从 Azure 门户的下拉菜单中选择所需的 SKU](media/change-sku/change-sku-selection.png)

更改 SKU 类型可能需要一两分钟的时间。

## <a name="next-steps"></a>后续步骤

如果你有一个资源林，并且想要在 SKU 更改后创建其他信任，请参阅[在 AZURE AD DS （预览版）中创建对本地域的出站林信任][create-trust]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
