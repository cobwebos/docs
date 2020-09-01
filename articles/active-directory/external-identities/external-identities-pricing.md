---
title: Azure AD 外部标识的 MAU 计费模型
description: 了解 Azure AD 每月活动用户 (MAU) 来宾用户协作的计费模型 (B2B) 在 Azure AD 中的详细信息。 了解如何将 Azure AD 租户链接到 Azure 订阅。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12aa400d6ca44043d3d90e78a93ae49d97a927e8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270020"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD 外部标识的计费模型

Azure Active Directory (Azure AD) 外部标识定价基于每月活动用户 (MAU) ，这是在日历月内具有身份验证活动的唯一用户的计数。 此计费模式适用于 Azure AD 来宾用户协作 (B2B) 和 [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/billing)。 通过提供免费层和灵活、可预测的定价，MAU 计费有助于降低成本。 本文介绍 MAU 计费，并将 Azure AD 租户链接到订阅。

> [!IMPORTANT]
> 本文不包含定价详细信息。 有关使用计费和定价的最新信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="what-do-i-need-to-do"></a>我需要做些什么？

若要利用 MAU 计费，你的 Azure AD 租户必须链接到 Azure 订阅。

|如果你的租户：  |你需要：  |
|---------|---------|
| 已链接到订阅的 Azure AD 租户     | 不执行任何操作。 使用外部标识功能与来宾用户协作时，将使用 MAU 模型自动向你计费。        |
| 尚未链接到订阅的 Azure AD 租户     | 将[Azure AD 租户链接到订阅](#link-your-azure-ad-tenant-to-a-subscription)，以激活 MAU 计费。        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>关于每月活动用户 (MAU) 计费

在 Azure AD 租户中，将根据在日历月内具有身份验证活动的唯一来宾用户计数，对来宾用户协作使用情况进行计费。 此模型将替换1:5 比率计费模型，该模型允许租户中的每个 Azure AD Premium 许可证最多允许5个来宾用户。 当你的租户链接到订阅，并使用外部标识功能与来宾用户协作时，将使用基于 MAU 的计费模型自动向你收费。
  
适用于来宾用户的定价层基于分配给 Azure AD 租户的最高定价层。 例如，如果你的租户中的最高定价层是 Azure AD Premium P1，则高级 P1 定价层还适用于你的来宾用户。 如果 Azure AD Free 最高定价，则当你尝试为来宾用户使用高级功能时，系统将要求你升级到 "高级" 定价层。

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>将 Azure AD 租户链接到订阅

必须将 Azure AD 租户链接到 Azure 订阅才能适当计费和访问功能。

1. 使用 Azure 帐户登录 [Azure 门户](https://portal.azure.com/) ，该帐户至少分配有订阅中的 [参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 角色或订阅中的资源组。

2. 选择包含你的订阅的目录：在 "Azure 门户" 工具栏中，选择 " **目录 + 订阅** " 图标，然后选择包含你的订阅的目录。

    ![选择 "目录 + 订阅" 图标](media/external-identities-pricing/portal-mau-pick-directory.png)

3. 在 " **Azure 服务**" 下，选择 **Azure Active Directory**。

4. 在左侧菜单中，选择“外部标识”。

5. 在 " **订阅**" 下，选择 " **链接的订阅**"。

6. 在 "租户" 列表中，选中租户旁边的复选框，然后选择 " **链接订阅**"。

    ![选择租户并链接订阅](media/external-identities-pricing/linked-subscriptions.png)

7. 在 "链接订阅" 窗格中，选择 **订阅** 和 **资源组**。 然后，选择“应用”。

    ![选择订阅和资源组](media/external-identities-pricing/link-subscription-resource.png)

完成这些步骤后，你的 Azure 订阅会根据 Azure Direct 或企业协议详细信息进行计费（如果适用）。

## <a name="next-steps"></a>后续步骤

有关最新定价信息，请参阅 [定价 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)。
