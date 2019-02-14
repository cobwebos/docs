---
title: 分配或删除许可证 - Azure Active Directory | Microsoft Docs
description: 关于如何对用户或组分配或删除 Azure Active Directory 许可证的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10346ea0aa292be33f820bd4e92434e2c58a89ab
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194302"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>使用 Azure Active Directory 门户分配或删除许可证 | Microsoft Docs
许多 Azure Active Directory (Azure AD) 服务要求激活 Azure AD 产品并且针对该产品向每个用户或组（以及关联成员）授予许可。 只有具有活动许可证的用户才能访问和使用已许可的 Azure AD 服务。

## <a name="available-product-editions"></a>可用产品版本
有多个版本可用于 Azure AD 产品。

- Azure AD Free

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

有关每个产品版本的特定信息和关联许可详细信息，请参阅[需要哪个许可证？](../authentication/concept-sspr-licensing.md)。

## <a name="view-your-product-edition-and-license-details"></a>查看产品版本和许可证详细信息
可以查看可用产品（包括各个许可证），检查是否存在任何即将到来的到期日期和可用分配数。

### <a name="to-find-your-product-and-license-details"></a>查找产品和许可证详细信息
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，然后选择“许可证”。

    “许可证”页随即出现。

    ![“许可证”页，显示已购买产品和已分配许可证数量](media/license-users-groups/license-details-blade.png)
    
3. 选择“已购买产品”链接可查看“产品”页，以及查看每个特定产品版本的“已分配”、“可用”和“即将过期”详细信息。

    ![“产品”页，包含产品版本和关联许可证信息](media/license-users-groups/license-products-blade-with-products.png)

4. 选择产品版本名称可查看其已许可的用户和组。

## <a name="assign-licenses-to-users-or-groups"></a>向用户或组分配许可证
确保使用已许可 Azure AD 服务的任何人都具有相应的许可证。 由你决定是要将许可权添加到单个用户还是整个组。

>[!Note]
>基于组的许可是 Azure AD 的一项公共预览版功能，可通过任何付费 Azure AD 许可证计划获得。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。<br><br>有关如何添加用户的详细信息，请参阅[如何在 Azure Active Directory 中添加或删除用户](add-users-azure-active-directory.md)。 有关如何创建组并添加成员的详细信息，请参阅[创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。

### <a name="to-assign-a-license-to-a-specific-user"></a>向特定用户分配许可证
1. 在“产品”页上，选择要分配给用户的版本的名称。 例如 Azure Active Directory Premium Plan 2。

    ![“产品”页，突出显示了产品版本](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. 在“Azure Active Directory Premium 计划 2”页上，选择“分配”。

    ![“产品”页，突出显示了“分配”选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. 在“分配”页上，选择“用户和组”，然后搜索并选择向其分配许可证的用户。 例如，Mary Parker。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-license-blade-with-highlight.png)

4. 选择“分配选项”，确保打开了相应的许可证选项，然后选择“确定”。

    ![“许可证选项”页，显示版本中的所有可用选项](media/license-users-groups/license-option-blade-assignments.png)

    “分配许可证”页会进行更新，以显示选择了用户并且配置了分配。

    >[!NOTE]
    >并非所有 Microsoft 服务都可在所有位置使用。 必须先指定“使用位置”，然后才能将许可证分配给用户。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”区域中设置此值。

5. 选择“分配”。

    用户会添加到已许可用户列表，并有权访问所包含的 Azure AD 服务。

### <a name="to-assign-a-license-to-an-entire-group"></a>向整个组分配许可证
1. 在“产品”页上，选择要分配给用户的版本的名称。 例如 Azure Active Directory Premium Plan 2。

    ![“产品”边栏选项卡，突出显示了产品版本](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. 在“Azure Active Directory Premium 计划 2”页上，选择“分配”。

    ![“产品”页，突出显示了“分配”选项](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. 在“分配”页上，选择“用户和组”，然后搜索并选择向其分配许可证的组。 例如 MDM policy - West。

    ![“分配许可证”页，突出显示了搜索和“选择”选项](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. 选择“分配选项”，确保打开了相应的许可证选项，然后选择“确定”。

    ![“许可证选项”页，显示版本中的所有可用选项](media/license-users-groups/license-option-blade-group-assignments.png)

    “分配许可证”页会进行更新，以显示选择了用户并且配置了分配。

    >[!NOTE]
    >并非所有 Microsoft 服务都可在所有位置使用。 必须先为所有成员指定“使用位置”，然后才能将许可证分配给组。 可以在 Azure AD 的“Azure Active Directory”&gt;“用户”&gt;“配置文件”&gt;“设置”区域中设置此值。 未指定使用位置的任何用户都会继承租户的位置。

5. 选择“分配”。

    组会添加到已许可组的列表，所有成员都有权访问所包含的 Azure AD 服务。


## <a name="remove-a-license"></a>删除许可证
可以通过“许可证”页从用户或组删除许可证。

### <a name="to-remove-a-license-from-a-specific-user"></a>从特定用户删除许可证
1. 在产品版本的“许可的用户”页上，选择应不再具有许可证的用户。 例如，Alain Charon。

2. 选择“删除许可证”。

    ![“许可的用户”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>从组删除许可证
1. 在产品版本的“许可的组”页上，选择应不再具有许可证的组。 例如 MDM policy - West。

2. 选择“删除许可证”。

    ![“许可的组”页，突出显示了“删除许可证”选项](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>无法直接删除用户从组继承的许可证。 而是必须从继承许可证的组删除用户。

## <a name="next-steps"></a>后续步骤
分配了许可证之后，可以执行以下过程：

- [识别和解决许可证分配问题](../users-groups-roles/licensing-groups-resolve-problems.md)

- [将许可的用户添加到组以进行许可](../users-groups-roles/licensing-groups-migrate-users.md)

- [在 Azure Active Directory 中使用组管理许可的方案、限制和已知问题](../users-groups-roles/licensing-group-advanced.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)
