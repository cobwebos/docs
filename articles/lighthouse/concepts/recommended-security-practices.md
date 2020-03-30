---
title: 建议的安全做法
description: 使用 Azure 委派的资源管理时，必须考虑安全性和访问控制。
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246902"
---
# <a name="recommended-security-practices"></a>建议的安全做法

使用[Azure 委派的资源管理](azure-delegated-resource-management.md)时，必须考虑安全性和访问控制。 租户中的用户将直接访问客户订阅和资源组，因此您需要采取措施维护租户的安全性。 您还需要确保仅允许有效管理客户资源所需的访问权限。 本主题提供相关建议，以帮助你实现此目的。

## <a name="require-azure-multi-factor-authentication"></a>需要 Azure 多重身份验证

[Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)（也称为两步验证）通过要求多个身份验证步骤有助于防止攻击者访问帐户。 你应要求服务提供商租户中的所有用户（包括任何将有权访问客户资源的用户）都进行多重身份验证。

建议你要求客户对其租户也实施 Azure 多重身份验证。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低权限原则向组分配权限

为了简化管理，我们建议对管理客户资源所需的每个角色使用 Azure AD 用户组。 这样，你就可以根据需要向组添加或删除单个用户，而不是直接向该用户分配权限。

> [!IMPORTANT]
> 为了添加 Azure AD 组的权限，**组类型**必须是 **"安全"，** 而不是**Office 365**。 创建组时选择此选项。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组以及添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

创建权限结构时，请确保遵循最低权限原则，以便用户仅具有完成作业所需的权限，这样有助于减少发生意外错误的几率。

例如，建议使用如下结构：

|组名称  |类型  |principalId  |角色定义  |角色定义 ID  |
|---------|---------|---------|---------|---------|
|架构师     |用户组         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|评估     |用户组         |\<principalId\>         |读取器         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 专家     |用户组         |\<principalId\>         |VM 参与者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自动化     |服务主体名称 (SPN)         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

创建这些组后，可以根据需要分配用户。 仅添加真正需要访问权限的用户。 请确保定期查看组成员身份，并删除不再适合或不再需要的任何用户。

请记住，[通过公共托管服务产品载入客户](../how-to/publish-managed-services-offers.md)时，包含的任何组（或用户或服务主体）都将具有与每位购买计划的客户的相同权限。 要分配不同的组以与每个客户一起使用，您需要使用 Azure 资源管理器模板发布每个客户或板载客户独有的单独私有计划。 例如，可以发布访问权限限制极少的公用计划，然后使用自定义 Azure 资源模板（可根据需要授予更多访问权限）直接与客户一起载入其资源以获取更多访问权限。


## <a name="next-steps"></a>后续步骤

- [部署 Azure 多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
