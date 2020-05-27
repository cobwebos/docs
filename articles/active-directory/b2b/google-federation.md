---
title: 将 Google 添加为 B2B 的标识提供者 - Azure AD
description: 与 Google 联合，使来宾用户能够使用其自己的 Gmail 帐户登录到你的 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1a743f313ca4159ce4ef03413a94ec25bb8fae
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591227"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>将 Google 添加为 B2B 来宾用户的标识提供者

通过设置 Google 联合，可让受邀的用户使用其自己的 Gmail 帐户登录到你的共享应用和资源，而无需创建 Microsoft 帐户 (MSA)。 

> [!NOTE]
> Google 联合专为 Gmail 用户设计。 若要与 G Suite 域联合，请使用[直接联合功能](direct-federation.md)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google 用户体验是什么？
向某个 Google Gmail 用户发送邀请时，该来宾用户应使用包含租户上下文的链接来访问你的共享应用或资源。 他们的体验根据是否已登录到 Google 而异：
  - 如果来宾用户未登录到 Google，则系统会提示他们登录到 Google。
  - 如果来宾用户已登录到 Google，则系统会提示他们选择要使用的帐户。 他们必须选择你在邀请他们时所用的帐户。

如果来宾用户看到“标头过长”错误，可以尝试清除其 Cookie，或者打开私用或 incognito 窗口，然后尝试登录。

![显示 Google“登录”页的屏幕截图](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>限制

Teams 完全支持所有设备上的 Google 来宾用户。 Google 用户可以从 `https://teams.microsoft.com` 等常见终结点登录到 Teams。

其他应用程序的常见终结点可能不支持 Google 用户。 Google 来宾用户必须使用包含租户信息的链接登录。 下面是一些示例：
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   如果 Google 来宾用户尝试使用 `https://myapps.microsoft.com` 或 `https://portal.azure.com` 之类的链接，则会收到错误。

还可以为 Google 来宾用户提供指向应用程序或资源的直接链接，只要此链接包含你的租户信息，例如 `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。 

## <a name="step-1-configure-a-google-developer-project"></a>步骤 1：配置 Google 开发人员项目
首先，在 Google Developers Console 中创建一个新项目，以获取稍后要添加到 Azure AD 的客户端 ID 和客户端机密。 
1. 转到 Google API (https://console.developers.google.com )，并使用 Google 帐户登录。 我们建议使用共享团队 Google 帐户。
2. 创建新项目：在仪表板上，依次选择“Create Project”（创建项目）、“Create”（创建）。  在“New Project”（新建项目）页上输入**项目名称**，然后选择“Create”（创建）。
   
   ![显示 Google“新建项目”页的屏幕截图](media/google-federation/google-new-project.png)

3. 确保已在项目菜单中选择该新项目。 然后在“API 和服务”下，选择“OAuth 许可屏幕”。

4. 依次选择“外部”、“创建”。 
5. 在“OAuth 许可屏幕”上，输入“应用程序名称”。 （保留其他设置。）

   ![显示 Google“OAuth 许可屏幕”选项的屏幕截图](media/google-federation/google-oauth-consent-screen.png)

6. 滚动到“授权域”部分，然后输入microsoftonline.com。

   ![显示“授权域”部分的屏幕截图](media/google-federation/google-oauth-authorized-domains.png)

7. 选择“保存”。

8. 选择“凭据”。 在“Create credentials”（创建凭据）菜单中，选择“OAuth client ID”（OAuth 客户端 ID）。 

   ![显示 Google API“创建凭据”选项的屏幕截图](media/google-federation/google-api-credentials.png)

9. 在“Application type”（应用程序类型）下，选择“Web application”（Web 应用程序），然后在“Authorized redirect URIs”（已授权的重定向 URI）下输入以下 URI：  
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>（其中，`<directory id>` 是你的目录 ID）
   
     > [!NOTE]
     > 若要查找自己的目录 ID，请转到 https://portal.azure.com ，然后在“Azure Active Directory”下选择“Properties”（属性）并复制“Directory ID”（目录 ID）。  

   ![显示“已授权的重定向 URI”部分的屏幕截图](media/google-federation/google-create-oauth-client-id.png)

10. 选择“创建”。 复制客户端 ID 和客户端机密，稍后在 Azure AD 门户中添加标识提供者时需要用到。

   ![显示 OAuth 客户端 ID 和客户端密钥的屏幕截图](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置 Google 联合 
现在请设置 Google 客户端 ID 和客户端机密：在 Azure AD 门户中输入，或者使用 PowerShell 进行设置。 请务必测试 Google 联合配置，方法是使用 Gmail 地址邀请自己，然后尝试使用受邀的 Google 帐户根据邀请操作。 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置 Google 联合 
1. 转到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”，然后单击“Google”按钮。
4. 输入名称。 然后输入前面获取的客户端 ID 和客户端机密。 选择“保存”。 

   ![显示“添加 Google 标识提供者”页的屏幕截图](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>使用 PowerShell 配置 Google 联合
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令：`Connect-AzureAD`。
3. 根据登录提示使用托管的全局管理员帐户登录。  
4. 运行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > 使用在“步骤 1：配置 Google 开发人员项目”中创建的应用的客户端 ID 和客户端密码。 有关详细信息，请参阅 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 一文。 
 
## <a name="how-do-i-remove-google-federation"></a>如何删除 Google 联合？
可以删除 Google 联合设置。 如果删除，则已按邀请操作的 Google 来宾用户将无法登录，但你可以通过从目录中删除然后重新邀请他们，使他们再次能够访问你的资源。 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中删除 Google 联合： 
1. 转到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”。
4. 在“Google”行上，选择上下文菜单（“...”），然后选择“删除”。 
   
   ![显示社交标识提供者“删除”选项的屏幕截图](media/google-federation/google-social-identity-providers.png)

1. 选择“是”以确认删除。 

### <a name="to-delete-google-federation-by-using-powershell"></a>使用 PowerShell 删除 Google 联合： 
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行 `Connect-AzureAD`。  
4. 根据登录提示使用托管的全局管理员帐户登录。  
5. 输入以下命令：

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 有关详细信息，请参阅 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。 
