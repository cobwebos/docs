---
title: 如何在 Azure Active Directory 中的其他组中添加或删除组 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 在其他组中添加或删除组。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: c28fe5ef226fac993fde221b16bfa875ba4845ca
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579762"
---
# <a name="how-to-add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>如何：使用 Azure Active Directory 在其他组中添加或删除组
本文可帮助使用 Azure Active Directory 在其他组中添加和删除组。

>[!Note]
>如果尝试删除父组，请参阅[如何更新或删除组及其成员](active-directory-groups-delete-group.md)。

## <a name="add-a-group-as-a-member-to-another-group"></a>将组作为成员添加到其他组
可以将现有组添加到其他现有组，创建成员组（子组）和父组。 成员组会继承父组的特性和属性，从而节省配置时间。

### <a name="to-add-a-group-as-a-member-to-another-group"></a>要将组作为成员添加到其他组

1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory”，然后选择“组”。

3. 在“组 - 所有组”页上，搜索并选择要成为其他组成员的组。 在本练习中，我们使用的是“MDM 策略 - 西部”组。

    >[!Note]
    >一次只可以将组作为成员添加到一个组。 此外，“选择组”框会根据条目与用户或设备名称的任何部分的匹配来筛选显示内容。 但是，不支持通配符。

    ![勾选了“MDM 策略 - 西部”组的“组 - 所有组”页](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. 在“MDM 策略 - 西部 - 组成员身份”页上，依次选择“组成员身份”、“添加”，找到希望组成为其成员的组，然后选中“选择”。 在本练习中，我们使用的是“MDM 策略 - 所有组织”组。

    “MDM 策略 - 西部”组现在是“MDM 策略 - 所有组织”组的成员，继承“MDM 策略 - 所有组织”组的所有属性和配置。

    ![通过将组添加到其他组来创建组成员身份](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. 查看“MDM 策略 - 西部 - 组成员身份”页，以查看组和成员关系。

    ![显示父组的“MDM 策略 - 西部 - 组成员身份”页](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. 有关组和成员关系的更加详细的视图，请选择组名称（“MDM 策略 - 所有组织”）并查看“MDM 策略 - 西部”页详细信息。

    ![显示成员和组详细信息的“组成员身份”页](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-member-group-from-another-group"></a>从其他组中删除成员组
可以从其他组中删除现有成员组。 但是，删除成员身份也会删除用户的所有继承特性和属性。

### <a name="to-remove-a-member-group-from-another-group"></a>要从其他组中删除成员组
1. 在“组 - 所有组”页上，搜索并选择要作为其他组成员进行删除的组。 在本练习中，我们再次使用“MDM 策略 - 西部”组。

2. 在“MDM 策略 - 西部概述”页上，选择“组成员身份”。

    ![“MDM 策略 - 西部概述”页](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. 从“MDM 策略 - 西部 - 组成员身份”页中选择“MDM 策略 - 所有组织”组，然后选择“MDM 策略 - 西部”页详细信息中的“删除”。

    ![显示成员和组详细信息的“组成员身份”页](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

- [查看组和成员](active-directory-groups-view-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [在组中添加或删除成员](active-directory-groups-members-azure-portal.md)

- [编辑组设置](active-directory-groups-settings-azure-portal.md)

- [按组向用户分配许可证](../users-groups-roles/licensing-groups-assign.md)
