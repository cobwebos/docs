---
title: Azure Active Directory B2C 的计费模型
description: 了解 Azure AD B2C 的月度活跃用户 (MAU) 计费模型，以及如何为特定的 Azure 订阅启用计费。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190001"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的计费模型

Azure Active Directory B2C (Azure AD B2C) 使用费从链接的 Azure 订阅中计收，采用月度活跃用户 (MAU) 计费模型。 请在以下部分中了解如何将 Azure AD B2C 资源链接到订阅，以及 MAU 计费模型的工作原理。

> [!IMPORTANT]
> 本文不包含定价信息。 有关用量计费和定价的最新信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="monthly-active-users-mau-billing"></a>月度活跃用户 (MAU) 计费

Azure AD B2C 计费按日历月中发生身份验证活动的唯一用户数计量，称为月度活跃用户 (MAU) 计费。

从 **2019 年 11 月 1 日**开始，所有新建的 Azure AD B2C 租户将按月度活跃用户 (MAU) 计费。 在 2019 年 11 月或之后[链接到订阅](#link-an-azure-ad-b2c-tenant-to-a-subscription)的现有租户将按月度活跃用户 (MAU) 计费。

如果你的现有 Azure AD B2C 租户在 2019 年 11 月 1 日之前已链接到订阅，可以选择采取以下措施之一：

* 升级到月度活跃用户 (MAU) 计费模型，或
* 继续使用按身份验证的计费模型

### <a name="upgrade-to-monthly-active-users-billing-model"></a>升级到月度活跃用户计费模型

对 Azure AD B2C 资源拥有管理访问权限的 Azure 订阅所有者可以切换到 MAU 计费模型。 计费选项在 Azure AD B2C 资源中进行配置。

切换到月度活跃用户 (MAU) 计费模型的操作**不可逆**。 将 Azure AD B2C 资源转换为基于 MAU 的计费模型后，无法将该资源还原为按身份验证的计费模型。

下面介绍如何将现有 Azure AD B2C 资源切换到 MAU 计费：

1. 以订阅所有者身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择要升级到 MAU 计费的 Azure AD B2C 目录。****<br/>
    ![Azure 门户中的目录和订阅筛选器](./media/billing/portal-mau-01-select-b2c-directory.png)
1. 在左侧菜单中，选择“Azure AD B2C”****。 或者，选择“所有服务”**** 并搜索并选择“Azure AD B2C”****。
1. 在 Azure AD B2C 租户的“概述”页上，选择“资源名称”下面的链接。******** 随后你将定向到 Azure AD 租户中的 Azure AD B2C 资源。<br/>
    ![Azure 门户中突出显示的 Azure AD B2C 资源链接](./media/billing/portal-mau-02-b2c-resource-link.png)
1. 在 Azure AD B2C 资源的“概述”页上的“计费单位”下，选择“按身份验证(更改为 MAU)”链接。************<br/>
    ![Azure 门户中突出显示的“更改为 MAU”链接](./media/billing/portal-mau-03-change-to-mau-link.png)
1. 选择“确认”完成升级到 MAU 计费。****<br/>
    ![Azure 门户中基于 MAU 的计费确认对话框](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>从按身份验证的计费过渡到 MAU 计费时预期发生的情况

在订阅/资源所有者确认更改后，即会启用基于 MAU 的计量。 每月帐单将反映做出此项更改之前计费的身份验证单位数，以及更改后开始产生的新 MAU 单位数。

在过渡月份不会对用户进行双重计数。 对于在做出更改之前进行身份验证的唯一活跃用户，将按日历月中的按身份验证费率收费。 订阅计费周期剩余时间的 MAU 计算不包括这些用户。 例如：

* Contoso B2C 租户包含 1,000 个用户。 其中 250 个用户在任意给定月份处于活跃状态。 在当月的 10 号，订阅管理员从按身份验证计费更改为月度活跃用户 (MAU) 计费。
* 在 1-10 号，将使用按身份验证模型计费。
  * 如果在此期间（1-10 号）有 100 个用户登录，这些用户将被标记为“月费已付”。**
* 从 10 号（过渡生效时间）开始，将按 MAU 费率计费。
  * 如果在此期间（10-30 号）另有 150 个用户登录，只对这额外的 150 个用户计费。
  * 前 100 个用户的持续活动不影响日历月剩余时间的计费。

在计费过渡期，订阅所有者可能会在其 Azure 订阅对帐单中同时看到两种方法（按身份验证和按 MAU）的条目：

* 其中一个条目是计费模型更改日期/时间之前的使用费，反映的是按身份验证计费。
* 另一个条目是做出更改之后的使用费，反映的是月度活跃用户 (MAU) 计费。

有关 Azure AD B2C 的用量计费和定价的最新信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>将 Azure AD B2C 租户链接到订阅

Azure Active Directory B2C (Azure AD B2C) 产生的使用费将从 Azure 订阅中计收。 创建 Azure AD B2C 租户后，租户管理员需要将 Azure AD B2C 租户显式链接到 Azure 订阅。

订阅链接是通过在目标 Azure 订阅中创建 Azure AD B2C*资源*来实现的。 可在单个 Azure 订阅中创建多个 Azure AD B2C 资源及其他 Azure 资源（例如虚拟机、存储帐户和逻辑应用）。 转到与订阅关联到的 Azure Active Directory (Azure AD) 租户可以查看该订阅中的所有资源。

链接到 Azure AD B2C 租户的订阅可用于对 Azure AD B2C 使用情况或其他 Azure 资源（包括其他 Azure AD B2C 资源）进行计费。 不能使用该订阅在 Azure AD B2C 租户中添加其他基于 Azure 许可证的服务或 Office 365 许可证。

### <a name="prerequisites"></a>先决条件

* [Azure 订阅](https://azure.microsoft.com/free/)
* 要链接到订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)
  * 你必须是租户管理员
  * 租户不能已链接到订阅

### <a name="create-the-link"></a>创建链接

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含要使用的 Azure 订阅的目录（不是包含 Azure AD B2C 租户的目录）。******
1. 选择“创建资源”，在“搜索市场”字段中输入 `Active Directory B2C`，然后选择“Azure Active Directory B2C”。************
1. 选择 **"创建"**
1. 选择“将现有 Azure AD B2C 租户链接到我的 Azure 订阅”。****
1. 从下拉列表中选择一个 **Azure AD B2C 租户**。 下拉列表中只显示你是其全局管理员且尚未链接到订阅的租户。 “Azure AD B2C 资源名称”字段中已预先填充所选 Azure AD B2C 租户的域名。****
1. 选择你是其管理员的有效 Azure **订阅**。
1. 在“资源组”下选择“新建”，然后指定“资源组位置”。************ 此处指定的资源组设置不会对 Azure AD B2C 租户位置、性能或计费状态造成影响。
1. 选择 **“创建”**。
    ![Azure 门户中的 Azure AD B2C 资源创建页](./media/billing/portal-01-create-b2c-resource-page.png)

针对 Azure AD B2C 租户完成这些步骤后，将会根据 Azure Direct 或企业协议详细信息（如果适用）在 Azure 订阅中计费。

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租户资源

在 Azure 订阅中创建 Azure AD B2C 资源后，应会看到连同其他 Azure 资源一起显示了“B2C 租户”类型的新资源。

使用此资源可以：

* 导航到该订阅以查看计费信息
* 获取 Azure AD B2C 租户的租户 ID（GUID 格式）
* 转到 Azure AD B2C 租户
* 提交支持请求
* 将 Azure AD B2C 租户资源移到另一个 Azure 订阅或资源组。

![Azure 门户中的 B2C 资源设置页](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>区域限制

如果针对在订阅中创建 Azure 资源的活动规定了区域限制，这种限制可能会阻止你创建 Azure AD B2C 资源。

若要解决此问题，请放宽区域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 云解决方案提供商 (CSP) 订阅

Azure AD B2C 中支持 Azure 云解决方案提供商 (CSP) 订阅。 可以使用 API 或 Azure 门户针对 Azure AD B2C 和所有 Azure 资源提供此功能。 CSP 订阅管理员可以链接、移动和删除与 Azure AD B2C 之间的关系，就像处理其他 Azure 资源一样。

使用基于角色的访问控制管理 Azure AD B2C 不受 Azure AD B2C 租户与 Azure CSP 订阅之间的关联影响。 基于角色的访问控制是使用租户基角色实现的，不是使用基于订阅的角色实现的。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>更改 Azure AD B2C 租户计费订阅

如果源订阅和目标订阅存在于同一 Azure Active Directory 租户中，则可以将 Azure AD B2C 租户移到另一个订阅。

若要了解如何将 Azure 资源（如 Azure AD B2C 租户）移到其他订阅，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

在开始移动之前，请务必阅读整篇文章，以充分了解此类移动的限制和要求。 除了移动资源的说明之外，它还包括一些重要信息，如移动前检查清单以及如何验证移动操作。

## <a name="next-steps"></a>后续步骤

有关最新的定价信息，请参阅[Azure 活动目录 B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
