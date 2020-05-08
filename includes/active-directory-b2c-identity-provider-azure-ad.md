---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82925443"
---
## <a name="register-an-azure-ad-app"></a>注册 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含组织 Azure AD 租户的目录（例如，contoso.com）。 在顶部菜单中选择 "**目录 + 订阅" 筛选器**，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择“新注册”。 
1. 输入应用程序的**名称**。 例如 `Azure AD B2C App`。
1. 仅对此应用程序接受**此组织目录中**的默认帐户选择。
1. 对于“重定向 URI”****，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`。

1. 选择“注册”  。 记录“应用程序(客户端) ID”，以便在后续步骤中使用****。
1. 依次选择“证书和机密”、“新建客户端机密”。********
1. 输入机密的**说明**，选择过期时间，然后选择 "**添加**"。 记录机密**值**以便在后面的步骤中使用。

### <a name="configuring-optional-claims"></a>配置可选声明

如果要从 Azure AD 获取`family_name`和`given_name`声明，则可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](/azure/active-directory/develop/active-directory-optional-claims)。

1. 登录 [Azure 门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”  。
1. 在“管理”部分，选择“应用注册”。********
1. 在列表中选择要为其配置可选声明的应用程序。
1. 从 "**管理**" 部分选择 "**令牌配置**"。
1. 选择“添加可选声明”。****
1. 对于 "**标记类型**"，选择 " **ID**"。
1. 选择要添加`family_name`的可选声明`given_name`。
1. 单击“添加”  。