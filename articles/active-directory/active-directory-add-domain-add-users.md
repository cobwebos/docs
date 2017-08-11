---
title: "将用户添加到 Azure Active Directory 中的自定义域 | Microsoft Docs"
description: "如何使用用户帐户填充 Azure Active Directory 中的自定义域。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 39cb54a6637088c35c6aef864a804c24803f48ba
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="assign-users-to-a-custom-domain"></a>将用户分配到自定义域
将自定义域添加到 Azure Active Directory 后，必须添加此域的用户帐户，才能开始对其身份验证。

> [!IMPORTANT]
> Microsoft 建议使用 Azure 门户而不是本文中引用的 Azure 经典门户通过 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。 有关如何在 Azure AD 管理中心管理域名的信息，请参阅[管理 Azure Active Directory 中的自定义域名](active-directory-domains-manage-azure-portal.md)。

## <a name="users-synced-from-a-on-premises-directory"></a>从本地目录同步的用户
如果已经设定本地 Active Directory 与 Azure Active Directory 之间的连接，则同步可以填充帐户。 如需有关如何同步 Azure Active Directory 与本地 Active Directory 的详细信息，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。

## <a name="users-added-and-managed-in-the-cloud"></a>在云中添加和管理的用户
若要更改现有用户帐户的域，请执行以下操作：

1. 使用全局管理员或用户管理员帐户打开 Azure 经典门户。
2. 打开目录。
3. 选择“用户”选项卡。
4. 从列表中选择用户。
5. 更改用户的域，并选择“保存”。

也可以使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或[图形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) 完成此操作。

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>创建新用户时选择自定义域
1. 使用全局管理员或用户管理员帐户打开 Azure 经典门户。
2. 打开目录。
3. 选择“用户”选项卡。
4. 在命令栏中，选择“添加”。
5. 添加用户名时，请从域列表中选择自定义域。
6. 选择“保存”。

## <a name="next-steps"></a>后续步骤
* [使用自定义域名来简化用户登录体验](active-directory-add-domain.md)
* [管理自定义域名](active-directory-add-manage-domain-names.md)
* [了解 Azure AD 中的域管理概念](active-directory-add-domain-concepts.md)


