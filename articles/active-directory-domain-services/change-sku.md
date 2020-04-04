---
title: 更改 Azure AD 域服务的 SKU |微软文档
description: 了解在业务需求发生变化时，如何访问 Azure AD 域服务托管域的 SKU 层
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655578"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>更改现有 Azure AD 域服务托管域的 SKU

在 Azure 活动目录域服务 （Azure AD DS） 中，可用性能和功能基于 SKU 类型。 这些功能差异包括备份频率或单向出站林信任的最大数量（当前处于预览中）。 创建托管域时选择 SKU，并且可以在部署托管域后，随着业务需求的变化而向上或向下切换 SKU。 业务需求的变化可能包括需要更频繁的备份或创建其他林信任。 有关不同 SKU 的限制和定价的详细信息，请参阅 Azure [AD DS SKU 概念][concepts-sku]和[Azure AD DS 定价][pricing]页。

本文演示如何使用 Azure 门户更改现有 Azure AD DS 托管域的 SKU。

## <a name="before-you-begin"></a>在开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。

## <a name="sku-change-limitations"></a>SKU 更改限制

在部署 Azure AD DS 托管域后，可以向上或向下更改 SKU。 但是，如果使用资源林（当前处于预览状态），并且创建了从 Azure AD DS 到本地 AD DS 环境的单向出站林信任，则 SKU 更改操作存在一些限制。 *高级**和企业*SKU 定义了可创建的信任数量的限制。 不能更改为最大限制低于当前配置的 SKU。

例如：

* 如果在*高级*SKU 上创建了两个林信任，则无法向下更改为*标准*SKU。 *标准*SKU 不支持林信任。
* 或者，如果您在*高级*SKU 上创建了七个信任，则无法向下更改为*企业*SKU。 *企业*SKU 最多支持五个信任。

有关这些限制的详细信息，请参阅 Azure [AD DS SKU 功能和限制][concepts-sku]。

## <a name="select-a-new-sku"></a>选择新的 SKU

要使用 Azure 门户更改 Azure AD DS 托管域的 SKU，请使用以下步骤完成：

1. 在 Azure 门户的顶部，搜索并选择**Azure AD 域服务**。 从列表中选择托管域，如*aaddscontoso.com*。
1. 在 Azure AD DS 页面左侧的菜单中，选择 **"设置"> SKU**。

    ![在 Azure 门户中选择 Azure AD DS 托管域的 SKU 菜单选项](media/change-sku/overview-change-sku.png)

1. 在下拉菜单中，为 Azure AD DS 托管域选择所需的 SKU。 如果您有资源林，则无法选择*标准*SKU，因为林信任仅在*企业*SKU 或更高版本上可用。

    从下拉菜单中选择您想要的 SKU，然后选择 **"保存**"。

    ![从 Azure 门户的下拉菜单中选择所需的 SKU](media/change-sku/change-sku-selection.png)

更改 SKU 类型可能需要一两分钟。

## <a name="next-steps"></a>后续步骤

如果您有资源林，并希望在 SKU 更改后创建其他信任，请参阅[在 Azure AD DS（预览）中创建对本地域的出站林信任][create-trust]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
