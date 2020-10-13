---
title: Azure Active Directory B2C 的计费模型
description: 了解 Azure AD B2C 的月度活跃用户 (MAU) 计费模型、如何将 Azure AD B2C 租户链接到 Azure 订阅，以及如何选择合适的高级层定价。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 416e2c767b5afd40fea38e6f75fcd3f01440b49a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255325"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的计费模型

Azure Active Directory B2C (Azure AD B2C) 定价基于月度活跃用户 (MAU)，这是对一个日历月内具有身份验证活动的独立用户的计数。 此计费模式适用于 Azure AD B2C 租户和 [Azure AD 来宾用户协作 (B2B) ](https://docs.microsoft.com/azure/active-directory/external-identities/external-identities-pricing)。 MAU 计费提供免费层和灵活且可预测的定价，有助于降低成本。 在本文中，你将了解 MAU 计费、如何将 Azure AD B2C 租户链接到订阅，以及如何更改你的定价层。

> [!IMPORTANT]
> 本文不包含定价详细信息。 有关用量计费和定价的最新信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="what-do-i-need-to-do"></a>我需要做些什么？

若要利用 MAU 计费，必须将你的 Azure AD B2C 租户链接到 Azure 订阅。 如果要使用 Azure AD B2C Premium P2 功能，例如基于风险的条件访问，则可能还需要将 Azure AD B2C 租户切换到其他定价层。

|如果你的租户是：  |你需要：  |
|---------|---------|
| 一个已经按 MAU 计费的 Azure AD B2C 租户     | 不执行任何操作。 当用户向你的 Azure AD B2C 租户进行身份验证时，系统会使用基于 MAU 的计费模型自动计费。        |
| 一个尚未链接到订阅的 Azure AD B2C 租户     |  [将你的 Azure AD B2C 租户链接到订阅](#link-an-azure-ad-b2c-tenant-to-a-subscription)，以激活 MAU 计费。     |
| 一个在 2019 年 11 月 1 日之前链接到订阅的 Azure AD B2C 租户    | [切换到 MAU 计费（推荐）](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)，或继续使用按身份验证的计费模型。     |
| 一个 Azure AD B2C 租户，并且你想要使用高级功能（例如基于风险的条件访问）    | [更改到支持你要使用的功能的 Azure AD 定价层](#change-your-azure-ad-pricing-tier)。        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>关于月度活跃用户 (MAU) 计费模型

MAU 计费已于 **2019 年 11 月 1 日**针对 Azure AD B2C 租户生效。 你在该日期或该日期之后创建并将其链接到某个订阅的任何 Azure AD B2C 租户都已按 MAU 计费。 如果你的 Azure AD B2C 租户尚未链接到订阅，则你现在需要执行该操作。 如果你有一个已在 2019 年 11 月 1 日之前链接到订阅的现有 Azure AD B2C 租户，我们建议你升级到月度活跃用户 (MAU) 计费模型，也可以继续使用按身份验证的计费模型。
  
你的 Azure AD B2C 租户还必须基于你要使用的功能链接到相应的 Azure 定价层。 高级功能需要 Azure AD B2C [高级 P1 或 P2 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。 使用新功能时，可能需要升级定价层。 例如，"条件性访问" 需要为租户选择 "Azure AD B2C 高级 P2" 定价层。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>将 Azure AD B2C 租户链接到订阅

Azure Active Directory B2C (Azure AD B2C) 产生的使用费将从 Azure 订阅中计收。 你需要通过在目标 Azure 订阅中创建 Azure AD B2C 资源，将 Azure AD B2C 租户显式链接到 Azure 订阅。 可在单个 Azure 订阅中创建多个 Azure AD B2C 资源及其他 Azure 资源（例如虚拟机、存储帐户和逻辑应用）。 转到与订阅关联到的 Azure Active Directory (Azure AD) 租户可以查看该订阅中的所有资源。

链接到 Azure AD B2C 租户的订阅可用于对 Azure AD B2C 使用情况或其他 Azure 资源（包括其他 Azure AD B2C 资源）进行计费。 不能使用该订阅在 Azure AD B2C 租户中添加其他基于 Azure 许可证的服务或 Office 365 许可证。

### <a name="prerequisites"></a>先决条件

* [Azure 订阅](https://azure.microsoft.com/free/)
* 要链接到订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)
  * 你必须是租户管理员
  * 租户不能已链接到订阅

### <a name="create-the-link"></a>创建链接

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含要使用的 Azure 订阅的目录（不是包含 Azure AD B2C 租户的目录）。******
3. 选择“创建资源”，在“搜索市场”字段中输入 `Active Directory B2C`，然后选择“Azure Active Directory B2C”。************
4. 选择“创建”。
5. 选择“将现有 Azure AD B2C 租户链接到我的 Azure 订阅”。****
6. 从下拉列表中选择一个 **Azure AD B2C 租户**。 只显示尚未链接到订阅且你是其全局管理员的租户。 “Azure AD B2C 资源名称”字段中已预先填充所选 Azure AD B2C 租户的域名。****
7. 选择你是其管理员的有效 Azure **订阅**。
8. 在“资源组”下选择“新建”，然后指定“资源组位置”。************ 此处指定的资源组设置不会对 Azure AD B2C 租户位置、性能或计费状态造成影响。
9. 选择“创建”。

    ![Azure 门户中的 Azure AD B2C 资源创建页](./media/billing/portal-01-create-b2c-resource-page.png)

当你针对 Azure AD B2C 租户完成这些步骤后，系统会根据 Azure Direct 或企业协议详细信息（如果适用）对你的 Azure 订阅计费。

## <a name="change-your-azure-ad-pricing-tier"></a>更改 Azure AD 定价层

租户必须根据你要对 Azure AD B2C 租户使用的功能链接到相应的 Azure 定价层。 高级功能需要 Azure AD B2C 高级 P1 或 P2，如 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中所述。 在某些情况下，使用新功能时需要升级定价层。 例如，如果想要使用标识保护、基于风险的条件性访问以及任何将来的高级 P2 功能与 Azure AD B2C，则需要为租户选择 Azure AD B2C 的高级 P2 定价层。

若要更改定价层，请执行以下步骤。

1. 登录到 Azure 门户。

2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择你的 Azure B2C 租户链接到的 Azure 订阅所在的目录（不要选择 Azure AD B2C 租户本身）。

3. 在门户顶部的“搜索”框中，输入你的 Azure AD B2C 租户的名称。 然后，在“资源”下的搜索结果中选择租户。

4. 在资源“概览”页上，在“定价层”下选择“更改”。

   ![更改定价层](media/billing/change-pricing-tier.png)
 
5. 选择包含你要启用的功能的定价层。

   ![选择定价层](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>切换到 MAU 计费（2019 年 11 月之前的 Azure AD B2C 租户）

如果你已在 **2019 年 11 月 1 日**之前将 Azure AD B2C 租户链接到订阅，则使用的是之前的按身份验证的计费模型。 建议升级到月度活跃用户 (MAU) 计费模型。 计费选项在 Azure AD B2C 资源中进行配置。

切换到月度活跃用户 (MAU) 计费模型的操作**不可逆**。 将 Azure AD B2C 资源转换为基于 MAU 的计费模型后，无法将该资源还原为按身份验证的计费模型。

下面介绍如何将现有 Azure AD B2C 资源切换到 MAU 计费：

1. 使用可以对 Azure AD B2C 资源进行管理访问的订阅所有者的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择要升级到 MAU 计费的 Azure AD B2C 目录。****<br/>

    ![Azure 门户中的目录和订阅筛选器](./media/billing/portal-mau-01-select-b2c-directory.png)

3. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。

4. 在 Azure AD B2C 租户的“概述”页上，选择“资源名称”下面的链接。******** 随后你将定向到 Azure AD 租户中的 Azure AD B2C 资源。<br/>

    ![Azure 门户中突出显示的 Azure AD B2C 资源链接](./media/billing/portal-mau-02-b2c-resource-link.png)

5. 在 Azure AD B2C 资源的“概述”页上的“计费单位”下，选择“按身份验证(更改为 MAU)”链接。************<br/>

    ![Azure 门户中突出显示的“更改为 MAU”链接](./media/billing/portal-mau-03-change-to-mau-link.png)

6. 选择“确认”完成升级到 MAU 计费。****<br/>

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

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租户资源

在 Azure 订阅中创建 Azure AD B2C 资源后，应会看到连同其他 Azure 资源一起显示了“B2C 租户”类型的新资源。

使用此资源可以：

* 导航到该订阅以查看计费信息
* 获取 Azure AD B2C 租户的租户 ID（GUID 格式）
* 转到 Azure AD B2C 租户
* 提交支持请求
* 将 Azure AD B2C 租户资源移到另一个 Azure 订阅或资源组。

### <a name="regional-restrictions"></a>区域限制

如果针对在订阅中创建 Azure 资源的活动规定了区域限制，这种限制可能会阻止你创建 Azure AD B2C 资源。

若要解决此问题，请放宽区域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 云解决方案提供商 (CSP) 订阅

Azure AD B2C 中支持 Azure 云解决方案提供商 (CSP) 订阅。 可以使用 API 或 Azure 门户针对 Azure AD B2C 和所有 Azure 资源提供此功能。 CSP 订阅管理员可以链接、移动和删除与 Azure AD B2C 之间的关系，就像处理其他 Azure 资源一样。

使用基于角色的访问控制管理 Azure AD B2C 不受 Azure AD B2C 租户与 Azure CSP 订阅之间的关联影响。 基于角色的访问控制是使用租户基角色实现的，不是使用基于订阅的角色实现的。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>更改 Azure AD B2C 租户计费订阅

### <a name="move-using-azure-resource-manager"></a>使用 Azure 资源管理器进行移动

如果源订阅和目标订阅存在于同一 Azure Active Directory 租户中，则可以使用 Azure 资源管理器将 Azure AD B2C 租户移到另一个订阅。

若要了解如何将 Azure 资源（如 Azure AD B2C 租户）移到其他订阅，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

在开始移动之前，请务必阅读整篇文章，以充分了解此类移动的限制和要求。 除了移动资源的说明之外，它还包括一些重要信息，如移动前检查清单以及如何验证移动操作。

### <a name="move-by-unlinking-and-relinking"></a>通过取消链接和重新链接进行移动

如果源订阅和目标订阅与不同的 Azure Active Directory 租户相关联，则无法通过 Azure 资源管理器执行移动，如上文所述。 但是，你仍然可以通过将 Azure AD B2C 租户从源订阅中取消链接并将其重新链接到目标订阅来获得相同的结果。 此方法是安全的，因为你删除的唯一对象是计费链接，而不是 Azure AD B2C 租户本身。 用户、应用、用户流等均不会受到影响。

1. 在 Azure AD B2C 目录本身中，从目标 Azure AD 租户（目标 Azure 订阅链接到的租户）[邀请来宾用户](user-overview.md#guest-user)，并确保此用户在 Azure AD B2C 中具有**全局管理员**角色。
1. 导航到表示源 Azure 订阅中的 Azure AD B2C 的 Azure 资源，如上面的[管理 Azure AD B2C 租户资源](#manage-your-azure-ad-b2c-tenant-resources)部分所述。 请勿切换到实际的 Azure AD B2C 租户。
1. 在“概览”页上，选择“删除”按钮。  这不会删除相关 Azure AD B2C 租户的用户或应用程序。 它仅删除源订阅中的计费链接。
1. 使用在步骤 1 的 Azure AD B2C 中作为管理员添加的用户帐户登录到 Azure 门户。 然后导航到目标 Azure 订阅，该订阅链接到目标 Azure Active Directory 租户。 
1. 按照上面的[创建链接](#create-the-link)过程，在目标订阅中重新建立计费链接。
1. 现在，你的 Azure AD B2C 资源已移动到目标 Azure 订阅（链接到目标 Azure Active Directory），并将从现在起通过此订阅进行计费。

## <a name="next-steps"></a>后续步骤

有关最新定价信息，请参阅[Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。