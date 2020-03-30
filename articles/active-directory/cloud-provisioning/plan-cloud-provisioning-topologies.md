---
title: Azure AD 连接云配置支持的拓扑和方案
description: 本主题介绍先决条件和硬件要求云配置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620875"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD 连接云配置支持的拓扑和方案
本文介绍了使用 Azure AD 连接云预配的各种本地和 Azure 活动目录 （Azure AD） 拓扑。 本文仅包括受支持的配置和方案。

> [!IMPORTANT]
> Microsoft 不支持在正式记录的配置或操作之外修改或操作 Azure AD Connect 云资源调配。 这些配置或操作中的任何一种都可能导致 Azure AD Connect 云配置的不一致或不受支持的状态。 因此，Microsoft 无法提供这种部署的技术支持。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>关于所有方案和拓扑要记住的事项
以下是选择解决方案时要记住的信息列表。

- 必须在所有林中唯一标识用户和组
- 云预配不会跨林匹配
- 用户或组必须在所有林中仅表示一次
- 对象的源锚点将自动选择。  它使用 ms-DS-一致性 Guid（如果存在），否则将使用 ObjectGUID。
- 不能更改用于源锚点的属性。

## <a name="single-forest-single-azure-ad-tenant"></a>单个林，单个 Azure AD 租户
![单个林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/single-forest.png)

最简单的拓扑是单个本地林，具有一个或多个域，以及单个 Azure AD 租户。  有关此方案的示例，请参阅[教程：具有单个 Azure AD 租户的单个林](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>多林、单个 Azure AD 租户
![多林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/multi-forest.png)

公共拓扑是多个 AD 林，具有一个或多个域，以及单个 Azure AD 租户。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD 连接的现有林，具有云预配的新林
![单个林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

此方案是拓扑类似于多林方案，但是此方案涉及现有的 Azure AD 连接环境，然后使用 Azure AD Connect 云预配引入新林。  有关此方案的示例，请参阅[教程：具有单个 Azure AD 租户的现有林](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>在现有混合 AD 林中试用 Azure AD 连接云资源调配
![单个林和单个租户](media/plan-cloud-provisioning-topologies/migrate.png)的拓扑 试验方案涉及在同一林中同时存在 Azure AD 连接和 Azure AD 连接云资源，并相应地对用户和组进行范围界定。 注： 对象应仅位于其中一个工具的作用域中。 

有关此方案的示例，请参阅[教程：在现有同步 AD 林中试用 Azure AD 连接云资源](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

