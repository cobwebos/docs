---
title: 设置 Azure Active Directory 组织的登录 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508424"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织

>[!NOTE]
> 此功能目前以公共预览版提供。 请勿在生产环境中使用该功能。

若要将 Azure Active Directory (Azure AD) 用作 Azure AD B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要创建一个表示它的应用程序。 本文介绍如何使用 Azure AD B2C 中的用户流为特定 Azure AD 组织中的用户启用登录。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在与 Azure AD B2C 租户不同的组织 Azure AD 租户中注册一个应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含你的 Azure AD 租户的目录。 选择**目录和订阅筛选器**顶部菜单中选择包含你的 Azure AD 租户的目录。 这不是与 Azure AD B2C 租户相同的租户。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
4. 选择“新注册”。 
5. 输入应用程序的名称。 例如，`Azure AD B2C App`。
6. 接受的选项**此组织目录中的帐户**为此应用程序。
7. 有关**重定向 URI**，接受的值**Web**，并输入以下 URL 中的所有小写字母，其中`your-B2C-tenant-name`将替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。

8. 单击“注册”  。 复制**应用程序 （客户端） ID**供稍后使用。
9. 选择**证书和机密**在应用程序菜单，然后选择**新的客户端机密**。
10. 输入客户端机密的名称。 例如，`Azure AD B2C App Secret`。
11. 选择的过期期限。 对于此应用程序，接受的选项**1 年内**。
12. 选择**添加**并复制显示，供以后的新客户端机密的值。

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

1. 请确保使用包含 Azure AD B2C 租户的目录。 选择**目录和订阅筛选器**顶部菜单中选择包含你的 Azure AD B2C 租户的目录。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
3. 选择“标识提供者”  ，然后选择“添加”  。
4. 输入“名称”  。 例如，输入 `Contoso Azure AD`。
5. 选择“标识提供者类型”，选择“Open ID 连接(预览版)”并单击“确定”    。
6. 选择**设置此标识提供者**
7. 对于“元数据 URL”  ，输入以下 URL，并将 `your-AD-tenant-domain` 替换为 Azure AD 租户的域名。 例如 `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. 有关**客户端 ID**，输入之前记录的应用程序 ID 和有关**客户端机密**，输入之前记录的客户端机密。
9. （可选）输入  Domain_hint 的值。 例如，`ContosoAD`。 在请求中使用 domain_hint 引用此标识提供者时会用到该值  。 
10. 单击“确定”。 
11. 选择“映射此标识提供者的声明”  并设置以下声明：
    
    - 在“用户 ID”中输入 `oid`  。
    - 在“显示名称”中输入 `name`  。
    - 在“名字”中输入 `given_name`  。
    - 在“姓氏”中输入 `family_name`  。
    - 在“电子邮件”中输入 `unique_name`  。

12. 单击“确定”，并单击“创建”保存配置。  
