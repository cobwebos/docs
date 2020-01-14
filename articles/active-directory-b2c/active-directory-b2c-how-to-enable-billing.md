---
title: Azure Active Directory B2C 的计费模式
description: 了解 Azure AD B2c 每月活动用户（MAU）计费模型，以及如何为特定的 Azure 订阅启用计费。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 32897994e4ef22845b1d19d6e0eb3e6b2c968c01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930731"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的计费模式

Azure Active Directory B2C （Azure AD B2C）使用情况按链接的 Azure 订阅计费，并使用每月活动用户（MAU）计费模式。 在以下部分中，了解如何将 Azure AD B2C 资源链接到订阅和 MAU 计费模型的工作方式。

> [!IMPORTANT]
> 本文不包含定价信息。 有关使用计费和定价的最新信息，请参阅[Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="monthly-active-users-mau-billing"></a>每月活动用户（MAU）计费

Azure AD B2C 计费按日历月（称为每月活动用户（MAU）计费）中具有身份验证活动的唯一用户计数计量。

从**01 年11月 2019**开始，所有新创建的 Azure AD B2C 租户都按每月活动用户计费（MAU）。 将按每月活动用户（MAU）对[链接到订阅](#link-an-azure-ad-b2c-tenant-to-a-subscription)（2019年11月1日或之后）的现有租户计费。

如果已有一个链接到2019年11月1日之前的订阅的现有 Azure AD B2C 租户，则可以选择执行下列操作之一：

* 升级到每月活动用户（MAU）计费模式，或
* 停留在每个身份验证计费模式上

### <a name="upgrade-to-monthly-active-users-billing-model"></a>升级到每月活动用户计费模式

对 Azure AD B2C 资源具有管理访问权限的 Azure 订阅所有者可以切换到 MAU 计费模型。 计费选项在 Azure AD B2C 资源中进行配置。

切换到每月活动用户（MAU）计费是不**可逆**的。 将 Azure AD B2C 资源转换为基于 MAU 的计费模型后，不能将该资源恢复为按身份验证计费模式。

下面介绍如何为现有 Azure AD B2C 资源切换到 MAU 计费：

1. 以订阅所有者的身份登录到[Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择要升级到 MAU 计费的 Azure AD B2C 目录。<br/>
    Azure 门户中的 ![目录和订阅筛选器](media/active-directory-b2c-how-to-enable-billing/portal-mau-01-select-b2c-directory.png)
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 在 Azure AD B2C 租户的 "**概述**" 页上，选择 "**资源名称**" 下的链接。 你将定向到 Azure AD 租户中的 Azure AD B2C 资源。<br/>
    Azure 门户中突出显示 Azure AD B2C 资源链接 ![](media/active-directory-b2c-how-to-enable-billing/portal-mau-02-b2c-resource-link.png)
1. 在 Azure AD B2C 资源的 "**概述**" 页上的 "**计费单位**" 下，选择 "**按身份验证（更改为 MAU）** " 链接。<br/>
    ![更改为 Azure 门户中突出显示的 MAU 链接](media/active-directory-b2c-how-to-enable-billing/portal-mau-03-change-to-mau-link.png)
1. 选择 "**确认**" 完成升级到 MAU 计费。<br/>
    Azure 门户中 ![基于 MAU 的计费确认对话框](media/active-directory-b2c-how-to-enable-billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>从按身份验证计费过渡到 MAU 计费时应发生的情况

一旦订阅/资源所有者确认了更改，就会立即启用基于 MAU 的计量。 每月帐单将反映在发生更改之前计费的身份验证单位，以及从更改开始的新的 MAU 单位。

在转换月期间，不会对用户进行双重计数。 在更改前进行身份验证的唯一活动用户在日历月中按身份验证费率收费。 对于订阅的其余部分，不会在 MAU 计算中包含这些相同的用户。 例如：

* Contoso B2C 租户包含1000个用户。 250用户在任何给定月份都处于活动状态。 订阅管理员从每个身份验证更改为每月10月活动用户（MAU）。
* 使用每个身份验证模型对第10天的帐单计费。
  * 如果100用户在此期间登录（第10天），则这些用户会被标记为*该月的付费*。
* 从第10个（过渡的有效时间）计费，按 MAU 费率计费。
  * 如果在此期间有其他150用户登录（第 10-30），则只对额外的150计费。
  * 前100个用户的继续活动不会影响日历月的剩余时间计费。

在过渡期间，订阅所有者可能会看到两个方法（每个身份验证和每个 MAU）的条目都显示在其 Azure 订阅计费声明中：

* 用于反映每个身份验证的更改日期/时间的情况的条目。
* 反映每月活动用户（MAU）的更改之后的使用条目。

有关 Azure AD B2C 的使用计费和定价的最新信息，请参阅[Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>将 Azure AD B2C 租户链接到订阅

Azure Active Directory B2C （Azure AD B2C）的使用费用按 Azure 订阅计费。 创建 Azure AD B2C 租户后，租户管理员需要将 Azure AD B2C 租户显式链接到 Azure 订阅。

订阅链接是通过在目标 Azure 订阅中创建 Azure AD B2C*资源*来实现的。 可以在单个 Azure 订阅中创建多个 Azure AD B2C 资源，以及其他 Azure 资源，如虚拟机、存储帐户和逻辑应用。 可以通过转到与订阅关联的 Azure Active Directory （Azure AD）租户来查看订阅中的所有资源。

链接到 Azure AD B2C 租户的订阅可用于 Azure AD B2C 使用情况或其他 Azure 资源的计费，包括附加 Azure AD B2C 资源。 不能使用该订阅在 Azure AD B2C 租户中添加其他基于 Azure 许可证的服务或 Office 365 许可证。

### <a name="prerequisites"></a>必备组件

* [Azure 订阅](https://azure.microsoft.com/free/)
* 要链接到订阅的[Azure AD B2C 租户](active-directory-b2c-get-started.md)
  * 你必须是租户管理员
  * 租户还不得链接到订阅

### <a name="create-the-link"></a>创建链接

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含要使用的 Azure 订阅的目录（*而不*是包含 Azure AD B2C 租户的目录）。
1. 选择 "**创建资源**"，在 "**搜索 Marketplace"** 字段中输入 `Active Directory B2C`，然后选择 " **Azure Active Directory B2C**"。
1. 选择“创建”
1. 选择 **"将现有 Azure AD B2C 租户链接到我的 Azure 订阅"** 。
1. 从下拉列表中选择**Azure AD B2C 租户**。 只显示你是其全局管理员并且尚未链接到订阅的租户。 " **Azure AD B2C 资源名称**" 字段中填充了所选 Azure AD B2C 租户的域名。
1. 选择你是其管理员的活动 Azure**订阅**。
1. 在 "**资源组**" 下，选择 "**新建**"，然后指定**资源组位置**。 此处的资源组设置不会影响 Azure AD B2C 租户位置、性能或计费状态。
1. 选择“创建”。
    ![Azure 门户中的 Azure AD B2C 资源创建页面](./media/active-directory-b2c-how-to-enable-billing/portal-01-create-b2c-resource-page.png)

为 Azure AD B2C 租户完成这些步骤后，你的 Azure 订阅会根据你的 Azure Direct 或企业协议详细信息进行计费（如果适用）。

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租户资源

在 Azure 订阅中创建 Azure AD B2C 资源后，应会看到 "B2C 租户" 类型的新资源与其他 Azure 资源一起出现。

使用此资源可以：

* 导航到订阅以查看计费信息
* 获取 GUID 格式的 Azure AD B2C 租户的租户 ID
* 转到 Azure AD B2C 租户
* 提交支持请求
* 将 Azure AD B2C 租户资源移到另一个 Azure 订阅或资源组

![Azure 门户中的 B2C 资源设置页](./media/active-directory-b2c-how-to-enable-billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>区域限制

如果已在订阅中建立 Azure 资源创建的区域限制，则该限制可能会阻止你创建 Azure AD B2C 资源。

若要缓解此问题，请放宽区域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 云解决方案提供商（CSP）订阅

Azure AD B2C 中支持 Azure 云解决方案提供商 (CSP) 订阅。 可以使用 API 或 Azure 门户针对 Azure AD B2C 和所有 Azure 资源提供此功能。 CSP 订阅管理员可以链接、移动和删除与其他 Azure 资源相同的 Azure AD B2C 关系。

使用基于角色的访问控制管理 Azure AD B2C 不受 Azure AD B2C 租户与 Azure CSP 订阅之间的关联影响。 基于角色的访问控制是使用基于租户的角色实现的，而不是基于订阅的角色。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>更改租户计费订阅 Azure AD B2C

如果源订阅和目标订阅存在于同一 Azure Active Directory 租户中，则可以将 Azure AD B2C 租户转移到另一个订阅。

若要了解如何将 Azure AD B2C 租户之类的 Azure 资源移到另一个订阅，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

开始移动之前，请务必阅读整篇文章，以充分了解此类移动的限制和要求。 除了移动资源的说明外，它还包含重要信息，如预移动清单和如何验证移动操作。

## <a name="next-steps"></a>后续步骤

除了查看所选 Azure 订阅中的使用情况和计费详细信息，还可以使用[使用情况报告 API](active-directory-b2c-reference-usage-reporting-api.md)查看详细的每日使用情况报表。
