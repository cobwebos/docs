---
title: Azure AD 域服务的具有作用域的同步 | Microsoft Docs
description: 了解如何使用 Azure 门户来配置从 Azure AD 到 Azure Active Directory 域服务托管域的区分范围的同步
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe373b9aa08d6999a9c57dd0008a207071e1dddd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068892"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 门户配置从 Azure AD 到 Azure Active Directory 域服务的区分范围的同步

为了提供身份验证服务，Azure Active Directory 域服务 (Azure AD DS) 从 Azure AD 同步用户和组。 在混合环境中，本地 Active Directory 域服务 (AD DS) 环境中的用户和组可以先使用 Azure AD Connect 同步到 Azure AD，然后再同步到 Azure AD DS 托管域。

默认情况下，Azure AD 目录中的所有用户和组都同步到托管域。 如果有特定需求，可以改为选择只同步所定义的一组用户。

本文展示了如何使用 Azure 门户配置区分范围的同步，然后更改或禁用区分范围的用户集合。 也可以[使用 PowerShell 完成这些步骤][scoped-sync-powershell]。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][tutorial-create-instance]的教程。
* 需要在 Azure AD 租户中拥有“全局管理员”特权才能更改 Azure AD DS 同步作用域。

## <a name="scoped-synchronization-overview"></a>具有作用域的同步概述

默认情况下，Azure AD 目录中的所有用户和组都同步到托管域。 如果只有几个用户需要访问托管域，则可以仅同步这些用户帐户。 此具有作用域的同步基于组。 配置基于组的具有作用域的同步时，只有属于指定组的用户帐户才会同步到托管域。 不同步嵌套组，只同步所选的特定组。

可以在创建托管域时或完成部署后更改同步范围。 现在还可以更改现有托管域上同步的范围，而无需进行重新创建。

若要详细了解同步过程，请参阅[了解 Azure AD 域服务中的同步][concepts-sync]。

> [!WARNING]
> 更改同步作用域会导致托管域重新同步所有数据。 请注意以下事项：
>
>  * 如果你更改托管域的同步范围，便会发生完全重新同步。
>  * 托管域中不再需要的对象会被删除。 托管域中会新建对象。

## <a name="enable-scoped-synchronization"></a>启用区分范围的同步

若要在 Azure 门户中启用区分范围的同步，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧菜单中选择“同步”。
1. 对于“同步类型”，请选择“区分范围”。
1. 选择“选择组”，搜索并选择要添加的组。
1. 进行所有更改后，选择“保存同步作用域”。

更改同步作用域会导致托管域重新同步所有数据。 托管域中不再需要的对象会被删除，重新同步可能需要一些时间才能完成。

## <a name="modify-scoped-synchronization"></a>修改具有作用域的同步

若要修改包含应同步到托管域的用户的组列表，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧菜单中选择“同步”。
1. 若要添加组，请选择顶部的“+ 选择组”，然后选择要添加的组。
1. 若要从同步作用域中删除某个组，请从当前同步的组列表中选择它，然后选择“删除组”。
1. 进行所有更改后，选择“保存同步作用域”。

更改同步作用域会导致托管域重新同步所有数据。 托管域中不再需要的对象会被删除，重新同步可能需要一些时间才能完成。

## <a name="disable-scoped-synchronization"></a>禁用具有作用域的同步

若要为托管域禁用基于组的具有作用域的同步，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧菜单中选择“同步”。
1. 将“同步类型”从“区分范围”更改为“全部”，然后选择“保存同步范围”  。

更改同步作用域会导致托管域重新同步所有数据。 托管域中不再需要的对象会被删除，重新同步可能需要一些时间才能完成。

## <a name="next-steps"></a>后续步骤

若要详细了解同步过程，请参阅[了解 Azure AD 域服务中的同步][concepts-sync]。

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
