---
title: 建议的安全做法
description: 使用 Azure 委托资源管理时，必须考虑安全性和访问控制。
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80246902"
---
# <a name="recommended-security-practices"></a>建议的安全做法

使用[Azure 委托资源管理](azure-delegated-resource-management.md)时，必须考虑安全性和访问控制。 你的租户中的用户将可以直接访问客户订阅和资源组，因此你需要采取措施来维护你的租户的安全性。 你还需要确保仅允许有效地管理客户资源所需的访问权限。 本主题提供相关建议，以帮助你实现此目的。

## <a name="require-azure-multi-factor-authentication"></a>需要 Azure 多重身份验证

[Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)（也称为双重验证）可帮助防止攻击者通过要求多个身份验证步骤来访问帐户。 你应要求服务提供商租户中的所有用户（包括任何将有权访问客户资源的用户）都进行多重身份验证。

建议你要求客户对其租户也实施 Azure 多重身份验证。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低权限原则向组分配权限

为了更轻松地进行管理，我们建议为管理客户的资源所需的每个角色使用 Azure AD 用户组。 这样，你就可以根据需要向组添加或删除单个用户，而不是直接向该用户分配权限。

> [!IMPORTANT]
> 若要为 Azure AD 组添加权限，“组类型”必须是“安全性”而不是“Office 365”  。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

创建权限结构时，请确保遵循最低权限原则，以便用户仅具有完成作业所需的权限，这样有助于减少发生意外错误的几率。

例如，建议使用如下结构：

|组名称  |类型  |principalId  |角色定义  |角色定义 ID  |
|---------|---------|---------|---------|---------|
|架构师     |用户组         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|评估     |用户组         |\<principalId\>         |读取器         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 专家     |用户组         |\<principalId\>         |VM 参与者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自动化     |服务主体名称 (SPN)         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

创建这些组后，可以根据需要分配用户。 仅添加真正需要访问权限的用户。 请确保定期查看组成员身份，并删除不再适合或不再需要的任何用户。

请记住，[通过公共托管服务产品载入客户](../how-to/publish-managed-services-offers.md)时，包含的任何组（或用户或服务主体）都将具有与每位购买计划的客户的相同权限。 要分配不同的组来与每个客户合作，你需要发布单独的私有计划，该计划独立于每个客户，或使用 Azure 资源管理器模板单独加入客户。 例如，可以发布访问权限限制极少的公用计划，然后使用自定义 Azure 资源模板（可根据需要授予更多访问权限）直接与客户一起载入其资源以获取更多访问权限。


## <a name="next-steps"></a>后续步骤

- [部署 Azure 多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
