---
title: 将属性同步到 Azure AD 进行映射 |微软文档
description: 了解如何将属性从本地活动目录同步到 Azure AD。 将用户预配配置为 SaaS 应用时，请使用目录扩展功能添加默认情况下未同步的源属性。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522265"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>将属性从本地活动目录同步到 Azure AD 以预配到应用程序

为用户预配自定义属性映射时，您可能会发现要映射的属性不会出现在 **"源"属性**列表中。 本文介绍如何通过将缺少的属性从本地活动目录 （AD） 同步到 Azure 活动目录 （Azure AD） 来添加它。

在将用户帐户从 Azure AD 预配到 SaaS 应用时，Azure AD 必须包含创建用户配置文件所需的所有数据。 在某些情况下，要使数据可用，可能需要将属性从本地 AD 同步到 Azure AD。 Azure AD Connect 会自动将某些属性同步到 Azure AD，但不是所有属性。 此外，默认情况下同步的某些属性（如 SAMAccountName）可能不会使用 Microsoft 图形 API 公开。 在这些情况下，可以使用 Azure AD 连接目录扩展功能将属性同步到 Azure AD。 这样，该属性将对 Microsoft 图形 API 和 Azure AD 预配服务可见。

如果需要预配的数据位于 Active Directory 中，但由于上述原因无法进行预配，请按照以下步骤操作。
 
## <a name="sync-an-attribute"></a>同步属性 

1. 打开 Azure AD 连接向导，选择任务，然后选择 **"自定义同步"选项**。

   ![Azure 活动目录连接向导 其他任务页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全局管理员身份登录。 

3. 在 **"可选功能"** 页上，选择**目录扩展属性同步**。
 
   ![Azure 活动目录连接向导 可选功能页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 选择要扩展到 Azure AD 的属性。
   > [!NOTE]
   > **"可用属性**"下的搜索区分大小写。

   ![Azure 活动目录连接向导目录扩展选择页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD 连接向导并允许运行完整的同步周期。 周期完成后，架构将扩展，并在本地 AD 和 Azure AD 之间同步新值。
 
6. 在 Azure 门户中，在[编辑用户属性映射](customize-application-attributes.md)时，**源属性**列表现在将包含格式`<attributename> (extension_<appID>_<attributename>)`中添加的属性。 选择该属性并将其映射到目标应用程序进行预配。

   ![Azure 活动目录连接向导目录扩展选择页](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 目前不支持从本地 AD（如**托管数据**或**DN/分辨名称**）预配引用属性的功能。 您可以在[用户语音](https://feedback.azure.com/forums/169401-azure-active-directory)上请求此功能。 

## <a name="next-steps"></a>后续步骤

* [定义谁在预配范围内](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
