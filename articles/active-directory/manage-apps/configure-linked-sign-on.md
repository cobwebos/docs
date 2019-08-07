---
title: Azure AD 应用程序的链接登录-Microsoft 标识平台 |Microsoft Docs
description: 将链接的单一登录 (SSO) 配置到 Microsoft 标识平台 (Azure AD) 中的 Azure AD 企业应用程序
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc1e26a3c7d284a60b830f6f66cdcecef97db4d6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834613"
---
# <a name="configure-linked-sign-on"></a>配置链接登录

添加库或非库 web 应用程序时, 可以使用的单一登录选项之一是[链接登录](what-is-single-sign-on.md)。 选择此选项可在组织的 Azure AD 访问面板或 Office 365 门户中添加指向应用程序的链接。 可以使用此方法将链接添加到当前使用 Active Directory 联合身份验证服务 (或其他联合身份验证服务) 而不是 Azure AD 进行身份验证的自定义 web 应用程序。 或者，可以添加特定 SharePoint 页面或想要在用户访问面板上显示的其他网页的深层链接。

## <a name="before-you-begin"></a>开始之前

如果尚未将应用程序添加到 Azure AD 租户，请参阅[添加库应用](add-gallery-app.md)或[添加非库应用](add-non-gallery-app.md)。

### <a name="open-the-app-and-select-linked-sign-on"></a>打开应用并选择 "链接登录"

1. 以 Azure AD 租户的云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到**Azure Active Directory** > **企业应用程序**。 此时会显示 Azure AD 租户中应用程序的随机示例。 

1. 在“应用程序类型”菜单中选择“所有应用程序”，然后选择“应用”。

1. 在“搜索”框中输入应用程序的名称，然后从结果中选择该应用程序。

1. 在“管理”部分选择“单一登录”。 

1. 选择 "**链接**"。

1. 输入要链接到的应用程序的 URL。 键入 URL, 然后选择 "**保存**"。 
 
1. 你可以将用户和组分配给应用程序, 这会导致应用程序出现在[Office 365 应用程序启动器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)中或这些用户的[Azure AD 访问面板](end-user-experiences.md)中。

1. 选择**保存**。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置用户帐户自动预配](configure-automatic-user-provisioning-portal.md)
