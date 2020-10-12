---
author: baanders
description: Azure 数字孪生安装程序中的步骤概述和权限必备组件包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009628"
---
>[!NOTE]
>这些操作旨在由有权管理 Azure 订阅上的资源和用户访问权限的用户完成。 尽管某些步骤可以使用较低权限完成，但需要具有这些权限的人员才能完全设置一个可用的实例。 有关详细信息，请查看下面的 [*先决条件： Required 权限*](#prerequisites-permission-requirements) 部分。

新的 Azure 数字孪生实例的完全安装由三个部分组成：
1. **创建实例**
2. **设置用户访问权限**： azure 用户需要具有 Azure 数字 *孪生所有者 (预览 * azure 数字孪生实例上的) 角色，才能管理该实例及其数据。 在此步骤中，你作为 Azure 订阅的所有者/管理员，会将此角色分配给将管理 Azure 数字孪生实例的人员。 这可能是你自己或组织中的其他人。
3. **设置客户端应用程序的访问权限**：通常，编写将用于与实例进行交互的客户端应用程序。 为了使该客户端应用能够访问 Azure 数字孪生，需要在客户端应用程序将用于向实例进行身份验证的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中设置*应用注册*。

若要继续，你将需要一个 Azure 订阅。 你可以在 [此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免费设置一个。

## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

若要完成本文中的所有步骤，需要 [在订阅中](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 拥有具有以下权限的角色：
* 创建和管理 Azure 资源
* 管理用户对 Azure 资源的访问权限， (包括授予和委派权限) 

满足此要求的常见角色包括 " *所有者*"、" *帐户管理员*" 或 " *用户访问管理员* " 和 " *参与者*" 的组合。 有关角色和权限的完整说明，包括其他角色包含哪些权限，请访问 Azure RBAC 文档中的 [*经典订阅管理员角色、Azure 角色和 Azure AD 角色*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 。

若要查看你在订阅中的角色，请访问 Azure 门户中的 " [订阅" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (你可以使用此链接或通过门户搜索栏) 查找 *订阅* 。 查找正在使用的订阅的名称，并在 " *我的角色* " 列中查看其角色：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Azure 门户中的 &quot;订阅&quot; 页的视图，将用户显示为所有者" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果发现值为 " *参与者*" 或另一个没有上述所需权限的角色，则可以联系订阅上具有这些权限的用户 (例如 *，订阅* 所有者或帐户管理员) ，并按以下方式之一进行操作：
* 请求用户以你的名义完成本文中的步骤
* 请求它们提升您在订阅上的角色，以便您有权自行进行。 这是否合适取决于你的组织和其中的角色。