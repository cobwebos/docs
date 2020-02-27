---
title: Azure AD Connect 云预配支持的拓扑和方案
description: 本主题介绍云预配的先决条件和硬件要求。
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
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620875"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect 云预配支持的拓扑和方案
本文介绍使用 Azure AD Connect 云预配的各种本地和 Azure Active Directory （Azure AD）拓扑。 本文仅包括支持的配置和方案。

> [!IMPORTANT]
> Microsoft 不支持在正式记录的配置或操作之外修改或操作 Azure AD Connect 云设置。 这些配置或操作中的任何一种都可能会导致 Azure AD Connect 云预配的状态不一致或不受支持。 因此，Microsoft 无法提供这种部署的技术支持。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>有关所有方案和拓扑的注意事项
下面是选择解决方案时要记住的信息列表。

- 必须跨所有林唯一标识用户和组
- 云预配不发生跨林匹配
- 用户或组必须在所有林中仅表示一次
- 将自动选择对象的源定位点。  它使用 Msds-consistencyguid （如果存在），否则使用 ObjectGUID。
- 不能更改用于源锚点的属性。

## <a name="single-forest-single-azure-ad-tenant"></a>单个林，单个 Azure AD 租户
![单个林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/single-forest.png)

最简单的拓扑是具有一个或多个域的单个本地林，以及单个 Azure AD 租户。  有关此方案的示例，请参阅[教程：包含单个 Azure AD 租户的单个林](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>多林、单个 Azure AD 租户
![多林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/multi-forest.png)

常见拓扑是一个多个 AD 林，其中包含一个或多个域，一个 Azure AD 租户。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD Connect 的现有林，具有云预配的新林
![单个林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

这种情况下，拓扑类似于多林方案，但这种情况涉及到现有 Azure AD Connect 环境，然后使用 Azure AD Connect 云预配引入新的林。  有关此方案的示例，请参阅[教程：包含单个 Azure AD 租户的现有林](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>试验 Azure AD Connect 现有混合 AD 林中的云预配
针对单个林和单个租户](media/plan-cloud-provisioning-topologies/migrate.png) 试验方案 ![拓扑涉及同一林中是否存在 Azure AD Connect 和 Azure AD Connect 云预配，并相应地设置用户和组的作用域。 注意：对象只应位于其中一个工具的范围内。 

有关此方案的示例，请参阅[教程：在现有同步的 AD 林中试点 Azure AD Connect 云预配](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

