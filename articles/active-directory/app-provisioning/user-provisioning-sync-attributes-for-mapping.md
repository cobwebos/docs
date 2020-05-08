---
title: 为映射将特性同步到 Azure AD
description: 了解如何将本地 Active Directory 的属性同步到 Azure AD。 配置 SaaS 应用的用户预配时，请使用目录扩展功能添加默认情况下不同步的源属性。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.openlocfilehash: 4b51d74d188f8f1c99f2075000e8fd91e70a81a4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593142"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>将属性从本地 Active Directory 同步到 Azure AD 以便预配到应用程序

为用户预配自定义属性映射时，您可能会发现您要映射的属性未出现在 "**源属性**" 列表中。 本文介绍如何通过将缺少的属性从本地 Active Directory （AD）同步到 Azure Active Directory （Azure AD）来添加该属性。

将用户帐户从 Azure AD 预配到 SaaS 应用时，Azure AD 必须包含创建用户配置文件所需的所有数据。 在某些情况下，若要使数据可用，可能需要将本地 AD 中的属性同步到 Azure AD。 Azure AD Connect 会自动将某些属性同步到 Azure AD，但不会将所有属性同步。 而且，默认情况下同步的某些属性（如 SAMAccountName）可能不会使用 Microsoft Graph API 公开。 在这些情况下，可以使用 Azure AD Connect 目录扩展功能将特性同步到 Azure AD。 这样一来，属性将对 Microsoft Graph API 和 Azure AD 预配服务可见。

如果预配所需的数据位于 Active Directory 中，但由于上述原因不可用于预配，请执行以下步骤。
 
## <a name="sync-an-attribute"></a>同步属性 

1. 打开 "Azure AD Connect" 向导，选择 "任务"，然后选择 "**自定义同步选项**"。

   ![Azure Active Directory Connect 向导 "其他任务" 页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全局管理员身份登录。 

3. 在 "**可选功能**" 页上，选择 "**目录扩展属性同步**"。
 
   ![Azure Active Directory Connect 向导的 "可选功能" 页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 选择要扩展到 Azure AD 的属性。
   > [!NOTE]
   > **可用属性**下的搜索区分大小写。

   ![Azure Active Directory Connect 向导目录扩展选择页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD Connect 向导并允许运行完全同步周期。 周期结束后，将扩展架构，并在本地 AD 与 Azure AD 之间同步新值。
 
6. 在 Azure 门户中，当您[编辑用户属性映射](customize-application-attributes.md)时，"**源属性**" 列表中现在将包含格式`<attributename> (extension_<appID>_<attributename>)`为的已添加属性。 选择属性，并将其映射到目标应用程序进行设置。

   ![Azure Active Directory Connect 向导目录扩展选择页](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 目前不支持从本地 AD （如**managedby**或**DN/DistinguishedName**）预配引用属性的功能。 可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求此功能。 

## <a name="next-steps"></a>后续步骤

* [定义要在范围内进行预配的人员](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
