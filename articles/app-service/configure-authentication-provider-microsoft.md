---
title: 配置 Microsoft 身份验证
description: 了解如何将 Microsoft 帐户身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 68af882bf240b354bdad1afe322135c048576ed4
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772830"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>将应用服务或 Azure Functions 应用配置为使用 Microsoft 帐户登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题说明如何将 Azure 应用服务或 Azure Functions 配置为使用 AAD 来支持个人 Microsoft 帐户登录。

> [!NOTE]
> 个人 Microsoft 帐户和组织帐户都使用 AAD 标识提供者。 目前无法将此标识提供者配置为支持这两种类型的登录。

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"></a>使用 Microsoft 帐户注册应用

1. 访问 Azure 门户中的[**应用注册**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。 如果需要，使用 Microsoft 帐户登录。
1. 选择“新注册”，然后输入应用程序名称。
1. 在“受支持的帐户类型”下，选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户和个人 Microsoft 帐户(例如，Skype、Xbox)”
1. 在“重定向 URI”下，选择“Web”，然后输入 `https://<app-domain-name>/.auth/login/aad/callback`。 将 *\<app-domain-name>* 替换为应用的域名。  例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 请务必在 URL 中使用 HTTPS 方案。

1. 选择“注册”。
1. 复制应用程序（客户端）ID。 稍后需要用到此信息。
1. 在左侧窗格中，选择“证书和机密” > “新建客户端机密”。 输入描述，选择有效期，然后选择“添加”。
1. 复制“证书和机密”页面上显示的值。 关闭页面后，就不再显示该值。

    > [!IMPORTANT]
    > 客户端机密值（密码）是非常重要的安全凭据。 请不要与任何人共享密码或者在客户端应用程序中分发它。

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"></a>向应用服务应用程序添加 Microsoft 帐户信息

1. 在 [Azure 门户]中，转到你的应用程序。
1. 选择“设置” > “身份验证/授权”，并确保“应用服务身份验证”为“启用”。
1. 在“身份验证提供程序”下，选择“Azure Active Directory”。 在“管理模式”下选择“高级”。 粘贴之前获取的应用程序（客户端）ID 和客户端机密。 对“颁发者 URL”字段使用 **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** 。
1. 选择“确定”。

   应用服务提供身份验证，但不限制对站点内容和 API 的授权访问。 必须在应用代码中为用户授权。

1. （可选）要限制只有 Microsoft 帐户用户可以访问，请将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 如果你设置此功能，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的请求都重定向为使用 AAD 进行身份验证。 请注意，由于你已将“颁发者 URL”配置为使用 Microsoft 帐户租户，因此只有个人帐户才能成功进行身份验证。

   > [!CAUTION]
   > 以这种方式限制访问权限适用于对应用的所有调用，但这对于有可公开访问的主页的应用（如许多单页应用）可能并不可取。 对于此类应用程序，“允许匿名请求(无操作)”可能是首选，以便应用本身手动启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。

现在，可以使用 Microsoft 帐户在应用中进行身份验证。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 门户]: https://portal.azure.com/
