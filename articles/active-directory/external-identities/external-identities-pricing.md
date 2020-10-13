---
title: Azure AD External Identities 的 MAU 计费模型
description: 了解 Azure AD 中来宾用户协作 (B2B) 的 Azure AD External Identities 月度活跃用户 (MAU) 计费模式。 了解如何将 Azure AD 租户关联到 Azure 订阅。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: d281b0ecd38ebbb76e093fd4f85213da4fc7d713
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265777"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD External Identities 的计费模型

Azure Active Directory (Azure AD) External Identities 定价基于月度活跃用户数 (MAU)，这是对一个日历月内具有身份验证活动的独立用户的计数。 此计费模型同时适用于 Azure AD 来宾用户协作 (B2B) 和 [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/billing)。 MAU 计费提供免费层和灵活且可预测的定价，有助于降低成本。 在本文中，你将了解 MAU 计费，以及如何将 Azure AD 租户关联到订阅。

> [!IMPORTANT]
> 本文不包含定价详细信息。 有关用量计费和定价的最新信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="what-do-i-need-to-do"></a>我需要做些什么？

若要利用 MAU 计费，必须将你的 Azure AD 租户关联到 Azure 订阅。

|如果你的租户是：  |你需要：  |
|---------|---------|
| 一个已关联到订阅的 Azure AD 租户     | 不执行任何操作。 如果使用 External Identities 功能与来宾用户协作，则将使用 MAU 模型自动计费。        |
| 一个尚未关联到订阅的 Azure AD 租户     | [将你的 Azure AD 租户关联到订阅](#link-your-azure-ad-tenant-to-a-subscription)，以激活 MAU 计费。        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>关于月度活跃用户 (MAU) 计费

在 Azure AD 租户中，将根据在日历月内具有身份验证活动的唯一来宾用户计数对来宾用户协作使用量进行计费。 此模型将替换 1:5 比率计费模型，该模型允许租户中的每个 Azure AD Premium 许可证最多 5 个来宾用户。 当你的租户链接到订阅时，如果使用 External Identities 功能与来宾用户协作，则将使用基于 MAU 的计费模型自动计费。
  
应用于来宾用户的定价层基于分配给 Azure AD 租户的最高定价层。 例如，如果你的租户中的最高定价层是 Azure AD Premium P1，则 Premium P1 定价层还应用于你的来宾用户。 如果最高定价是 Azure AD Free，则当你尝试为来宾用户使用高级功能时，系统会要求你升级到高级定价层。

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>将 Azure AD 租户关联到订阅

必须将 Azure AD 租户关联到 Azure 订阅才能正确计费并访问功能。 如果目录还没有可以链接到的订阅，则可以在此过程中添加一个订阅。

1. 使用至少在订阅中分配了[参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)角色的 Azure 帐户或订阅中的资源组登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择要链接的目录：在 "Azure 门户" 工具栏中，选择 " **目录 + 订阅** " 图标，然后选择目录。

    ![选择“目录 + 订阅”图标](media/external-identities-pricing/portal-mau-pick-directory.png)

3. 在“Azure 服务”下，选择“Azure Active Directory”。

4. 在左侧菜单中，选择“外部标识”。

5. 在“订阅”下，选择“关联的订阅”。 

6. 在租户列表中，选中租户旁边的复选框，然后选择“关联订阅”。

    ![选择租户并关联订阅](media/external-identities-pricing/linked-subscriptions.png)

7. 在“关联订阅”窗格中，选择一个**订阅**和一个**资源组**。 然后，选择“应用”。

   > [!NOTE]
   > 如果未列出订阅，可以 [将订阅关联到租户](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。 或者， **如果你**还没有订阅，可以通过选择链接添加新订阅。

    ![选择一个订阅和一个资源组](media/external-identities-pricing/link-subscription-resource.png)

完成这些步骤后，系统会根据 Azure Direct 或企业协议详细信息（如果适用）对你的 Azure 订阅计费。

## <a name="next-steps"></a>后续步骤

有关最新定价信息，请参阅[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。
