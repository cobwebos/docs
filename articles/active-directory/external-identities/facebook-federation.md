---
title: 将 Facebook 添加为标识提供者 - Azure AD
description: 与 Facebook 联合，让外部用户（来宾）能够使用自己的 Facebook 帐户登录 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5e1db2c86f6118c3cd333974c9cfd64f747128
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908290"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>将 Facebook 添加为外部标识的标识提供者

可以将 Facebook 添加到自助注册用户流（预览），这样用户可以使用自己的 Facebook 帐户来登录应用。 必须先为租户[启用自助注册](self-service-sign-up-user-flow.md)，然后才能允许用户使用 Facebook 登录。 将 Facebook 添加为标识提供者后，设置应用的用户流，并选择“Facebook”作为登录方式之一。

> [!NOTE]
> 用户只能使用其 Facebook 帐户通过支持自助服务注册和用户流的应用进行注册。 用户无法通过 Facebook 帐户被邀请或兑换邀请。

## <a name="create-an-app-in-the-facebook-developers-console"></a>在 Facebook 开发人员控制台中创建应用

必须在 Facebook 开发人员控制台中创建应用，才能将 Facebook 帐户用作[标识提供者](identity-providers.md)。 如果还没有 Facebook 帐户，可以在 [https://www.facebook.com/](https://www.facebook.com) 处注册。

> [!NOTE]  
> 在下面的第 9 步和第 16 步中使用以下 URL。
> - 对于 "**站点 URL** "，请输入应用程序的地址，例如 `https://contoso.com` 。
> - 对于“有效 OAuth 重定向 URI”，输入“`https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`”。 可以在“Azure Active Directory 概述”边栏选项卡中找到 `<tenant-ID>`。


1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
2. 如果以前没有登录过，需要注册为 Facebook 开发人员。 为此，请选择页面右上角的“开始使用”，接受 Facebook 的策略，然后完成注册步骤。
3. 依次选择“我的应用”和“创建应用”。
4. 输入“显示名称”和有效的“联系人电子邮件”。
5. 选择“创建应用 ID”。 这会要求接受 Facebook 平台策略并完成在线安全检查。
6. 选择“设置” > “基本”。
7. 选择“类别”（例如“业务”和“网页”）。 Facebook 需要这个值，但 Azure AD 不需要。
8. 在页面底部，选择“添加平台”，然后选择“网站”。
9. 在“站点 URL”中，输入相应的 URL（如上所述）。
10. 在“隐私策略 URL”中，输入用于维护应用隐私信息的页面的 URL（例如“`http://www.contoso.com`”）。
11. 选择“保存更改”。
12. 在页面的顶部，复制“应用 ID”的值。
13. 选择“显示”，然后复制“应用密码”的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据。
14. 依次选择“产品”旁边的加号，以及“Facebook 登录”下的“设置”。
15. 选择“Facebook 登录”下的“设置”。
16. 在“有效 OAuth 重定向 URI”中，输入相应的 URL（如上所述）。
17. 选择页面底部的“保存更改”。
18. 若要让 Facebook 应用对 Azure AD 可用，请选择页面右上角的“状态”选择器，将它设置为“开”以公开应用，然后选择“切换模式”。 此时，状态应从“开发”变为“实时” 。
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>将 Facebook 帐户配置为标识提供者
现在请设置 Facebook 客户端 ID 和客户端密码：在 Azure AD 门户中输入，或者使用 PowerShell 进行设置。 可以通过以下方法测试 Facebook 配置：通过在已启用了自助服务注册的应用上的用户流进行注册。

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置 Facebook 联合身份验证
1. 以 Azure AD 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 依次选择“所有标识提供者”和“Facebook”。
5. 对于“客户端 ID”，输入之前创建的 Facebook 应用的“应用 ID”。
6. 对于“客户端密码”，输入已记录的“应用密码”。

   ![显示“添加社交标识提供者”页的屏幕截图](media/facebook-federation/add-social-identity-provider-page.png)

7. 选择“保存”。
### <a name="to-configure-facebook-federation-by-using-powershell"></a>使用 PowerShell 配置 Facebook 联合身份验证
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令：`Connect-AzureAD`。
3. 根据登录提示使用托管的全局管理员帐户登录。  
4. 运行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > 使用在 Facebook 开发者控制台中创建的应用客户端 ID 和客户端密码。 有关详细信息，请参阅 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 一文。 

## <a name="how-do-i-remove-facebook-federation"></a>如何删除 Facebook 联合身份验证？
可以删除 Facebook 联合身份验证设置。 这样做的话，任何已经在用户流中使用 Facebook 帐户注册的用户都将无法再登录。 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中删除 Facebook 联合身份验证： 
1. 转到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”。
4. 在“Facebook”行上，选择上下文菜单（“...”），然后选择“删除”。 
5. 选择“是”以确认删除。

### <a name="to-delete-facebook-federation-by-using-powershell"></a>使用 PowerShell 删除 Facebook 联合身份验证： 
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行 `Connect-AzureAD`。  
4. 根据登录提示，使用托管的全局管理员帐户登录。  
5. 输入以下命令：

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > 有关详细信息，请参阅 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。 

## <a name="next-steps"></a>后续步骤

- [向应用添加自助注册](self-service-sign-up-user-flow.md)
