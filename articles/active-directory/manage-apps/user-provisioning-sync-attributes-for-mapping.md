---
title: 同步到 Azure AD 进行映射的属性 |Microsoft Docs
description: 了解如何同步到 Azure AD 从本地 Active Directory 属性。 在配置用户预配到 SaaS 应用时，使用的目录扩展功能添加默认情况下不同步的源属性。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806111"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>同步到 Azure AD 预配到应用程序从本地 Active Directory 属性

自定义用户预配属性映射，你可能会发现，你想要映射的属性不会显示在**源属性**列表。 本文介绍如何通过从你的本地 Active Directory (AD) 同步到 Azure Active Directory (Azure AD) 中添加缺少的属性。

Azure AD 必须包含预配用户帐户从 Azure AD 对 SaaS 应用程序时创建用户配置文件所需的所有数据。 在某些情况下，以使数据可您可能需要同步属性从你的本地 AD 到 Azure AD。 Azure AD Connect 会自动同步到 Azure AD 中，某些属性，但并非所有属性。 此外，默认情况下同步某些属性 （例如 SAMAccountName) 可能不会通过 Azure AD 图形 API 公开。 在这些情况下，可以使用 Azure AD Connect 目录扩展功能来同步到 Azure AD 的属性。 这样一来，该属性将显示 Azure AD Graph API 和 Azure AD 预配服务。

如果预配所需的数据是在 Active Directory 中，但不可用于预配上述原因，请执行以下步骤。
 
## <a name="sync-an-attribute"></a>同步属性 

1. 打开 Azure AD Connect 向导中，选择任务，并选择**自定义同步选项**。

   ![Azure Active Directory Connect 向导的其他任务页](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全局管理员身份登录。 

3. 上**可选功能**页上，选择**目录扩展属性同步**。
 
   ![Azure Active Directory Connect 向导的可选功能页](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 选择你想要扩展到 Azure AD 的属性。
   > [!NOTE]
   > 在搜索**可用的属性**区分大小写。

   ![Azure Active Directory Connect 向导目录扩展选择页](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD Connect 向导，并允许运行完全同步周期。 周期完成后，扩展了架构和新值之间的本地 AD 和 Azure AD 同步。
 
6. 在 Azure 门户中，您可以在[编辑用户属性映射](customize-application-attributes.md)，则**源属性**列表现在将包含添加的属性格式`<attributename> (extension_<appID>_<attributename>)`。 选择该属性并将其映射到预配的目标应用程序。

   ![Azure Active Directory Connect 向导目录扩展选择页](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 设置从引用属性的功能在本地 AD 中，如**managedby**或**DN/DistinguishedName**，目前不支持。 可以在请求此功能[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)。 

## <a name="next-steps"></a>后续步骤

* [定义谁是预配范围内](define-conditional-rules-for-provisioning-user-accounts.md)
