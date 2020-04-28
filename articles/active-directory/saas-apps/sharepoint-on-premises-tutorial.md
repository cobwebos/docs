---
title: 教程：Azure Active Directory 与本地 SharePoint 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与本地 SharePoint 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867104"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>教程：Azure Active Directory 与本地 SharePoint 的单一登录 (SSO) 集成

本教程介绍如何将本地 SharePoint 与 Azure Active Directory (Azure AD) 集成。 将本地 SharePoint 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问本地 SharePoint。
* 让用户使用其 Azure AD 帐户自动登录到本地 SharePoint。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure Active Directory 与本地 SharePoint 的集成，需要准备好以下各项：

* 一个 Azure Active Directory 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 一个 SharePoint 2013 场或更高版本的场。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure Active Directory 单一登录。 Azure Active Directory 中的用户应可以访问你的本地 Sharepoint。

## <a name="create-the-enterprise-applications-in-azure-portal"></a>在 Azure 门户中创建企业应用程序

若要配置本地 SharePoint 与 Azure AD 的集成，需要从库中将本地 SharePoint 添加到托管 SaaS 应用列表。

若要从库中添加本地 SharePoint，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

 > [!NOTE]
 > 如果元素应不可用，也可以通过左侧导航面板顶部的固定“所有服务”  链接将其打开。 在以下概述中，“Azure Active Directory”  链接位于“标识”  部分中，或者可以使用筛选器文本框搜索它。

2. 转到“企业应用”，并选择“所有应用”选项   。

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

4. 在搜索框中键入“本地 SharePoint”，从结果面板中选择“本地 SharePoint”。  

    <kbd>![结果列表中的“本地 SharePoint”](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. 指定本地 SharePoint 的名称，然后单击“添加”按钮以添加该应用程序。 

1. 在新企业应用程序中单击“属性”，然后检查“需要用户分配”的值 

   <kbd>![结果列表中的“本地 SharePoint”](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

在我们的方案中，此值设置为“否”。 

## <a name="configure-and-test-azure-ad"></a>配置和测试 Azure AD

在本部分，你将配置本地 SharePoint 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与本地 SharePoint 相关用户之间建立链接关系。

若要配置和测试本地 SharePoint 的 Azure Active Directory 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[配置本地 SharePoint](#configure-sharepoint-on-premises)** - 在应用程序端配置单一登录设置。
1. **[在 Azure 门户中创建 Azure AD 测试用户](#create-an-azure-ad-test-user-in-the-azure-portal)** - 在 Azure AD 中创建用于单一登录的新用户。
1. **[在 Azure 门户中创建 Azure AD 安全组](#create-an-azure-ad-security-group-in-the-azure-portal)** - 在 Azure AD 中创建用于单一登录的新安全组。
1. **[向本地 SharePoint 中的 Azure Active Directory 帐户授予权限](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** - 向 Azure AD 用户授予权限。
1. **[向本地 SharePoint 中的 Azure AD 组授予权限](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** - 向 Azure AD 组授予权限。
1. **[在 Azure 门户中向来宾帐户授予对本地 SharePoint 的访问权限](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** - 向 Azure AD 中的来宾帐户授予对本地 SharePoint 的权限。
1. **[为多个 Web 应用程序配置受信任的标识提供者](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** - 如何对多个 Web 应用程序使用相同的受信任标识提供者

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置本地 SharePoint 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中打开 Azure AD 目录，单击“企业应用程序”，单击前面创建的企业应用程序名称，然后单击“单一登录”。   

2. 在“选择单一登录方法”对话框中，单击“SAML”模式以启用单一登录   。
 
3. 在“设置 SAML 单一登录”页上，单击“编辑”图标打开“基本 SAML 配置”对话框    。

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![本地 SharePoint 域和 URL 单一登录信息](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. 在“标识符”框中，使用以下模式键入 URL：`urn:<sharepointFarmName>:<federationName>`

    1. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<YourSharePointSiteURL>/_trust/`

    1. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YourSharePointSiteURL>/`。
    1. 单击“保存”。

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. 在“设置本地 SharePoint”部分，根据要求复制相应 URL  。
    
    1. **登录 URL**
    
        复制登录 URL。请将末尾的 /saml2 替换为 /wsfed，使该 URL 看起来类似于 https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed （此 URL 并不准确）   

    1. **Azure AD 标识符**
    1. **注销 URL**
    > [!NOTE]
    > 不能在 SharePoint 中按原样使用此 URL：必须将 /saml2 替换为 /wsfed。   本地 SharePoint 应用程序使用 SAML 1.1 令牌，因此，Azure AD 预期 WS 联合身份验证请求来自 SharePoint 服务器；身份验证后，它会颁发 SAML 1.1 令牌。

### <a name="configure-sharepoint-on-premises"></a>配置本地 SharePoint

1. **在 SharePoint Server 2016 中创建新的受信任标识提供者**

    登录到 SharePoint 服务器并打开 SharePoint Management Shell。 填写以下值：
    1. **$realm**（Azure 门户中“本地 SharePoint 域和 URL”部分中显示的“标识符”值）。
    1. **$wsfedurl**（单一登录服务 URL）。
   1. **$filepath**（已从 Azure 门户中将证书文件下载到的文件路径）。

    运行以下命令配置新的受信任标识提供者。

    > [!TIP]
    > 如果不熟悉 PowerShell 的用法，或想要详细了解 PowerShell 的工作原理，请参阅 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)。


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **为应用程序启用受信任的标识提供者**

    a. 在管理中心，导航到“管理 Web 应用程序”并选择要使用 Azure AD 保护的 Web 应用程序。 

    b. 在功能区中单击“身份验证提供程序”，然后选择要使用的区域。 

    c. 选择“受信任的标识提供者”，然后选择刚刚注册的名为 *AzureAD* 的标识提供者。 

    d. 单击“确定”。 

    ![配置身份验证提供程序](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”  ，并在“管理”  窗格中选择“用户”  。

2. 然后在屏幕顶部依次选择“所有用户”、“新建用户”。  

3. 选择“创建用户”选项，并在“用户属性”中执行以下步骤。   
   可能可以使用租户后缀或任何已验证的域在 Azure AD 中创建用户。 

    a. 在“名称”字段中输入用户名，我们使用了“TestUser”。  
  
    b. 在“用户名”字段中，键入 `TestUser@yourcompanydomain.extension`  
    例如： TestUser@contoso.com

    ![“用户”对话框](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

    e. 现在可以与 TestUser@contoso.com 共享网站，并允许此用户访问该网站。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 安全组

1. 单击“Azure Active Directory”>“组”。 

2. 单击“新建组”： 

3. 填写“组类型”、“组名称”、“组说明”和“成员资格类型”。     单击箭头选择成员，然后搜索或单击要添加到该组的成员。 单击“选择”以添加选定的成员，然后单击“创建”。  

![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>向本地 SharePoint 中的 Azure Active Directory 帐户授予权限

若要向本地 SharePoint 中的 Azure Active Directory 用户授予访问权限，需要共享网站集，或者将 Azure Active Directory 用户添加到网站集的某个组中。用户现在可以使用 Azure AD 中的标识登录到 SharePoint 201x，但仍存在改善用户体验的机会。 例如，搜索某个用户会在人员选取器中显示多个搜索结果。 在声明映射中创建的每个声明类型都有一个搜索结果。 若要使用人员选取器选择某个用户，必须准确键入其用户名，并选择“名称”声明结果。 

![声明搜索结果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

系统不会验证要搜索的值，因此，可能会出现拼写错误，或者用户意外选择错误声明类型的情况。 这可能会导致用户无法成功访问资源。

为了解决此方案的人员选取器问题，名为 [AzureCP](https://yvand.github.io/AzureCP/) 的开源解决方案为 SharePoint 2013、2016 和 2019 提供了自定义的声明提供程序。  它将使用 Microsoft Graph API 来解析进入并执行验证功能的用户。 详细了解 [AzureCP](https://yvand.github.io/AzureCP/)。

  > [!NOTE]
  > 如果没有 AzureCP，可以通过添加 Azure AD 组的 ID 来添加组，但此方法不具备用户友好性，且不可靠。 操作方法如下所示：  
  >   
  >![将 Azure AD 组添加到 Sharepoint 组](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>向本地 SharePoint 中的 Azure AD 组授予权限

若要将 Azure Active Directory 安全组分配给本地 SharePoint，必须使用适用于 SharePoint Server 的自定义声明提供程序。 我们的示例使用了 AzureCP。

 > [!NOTE]
 > 请注意，AzureCP 不是 Microsoft 产品，Microsoft 技术支持部门不会为其提供支持。 根据 https://yvand.github.io/AzureCP/ 中所述，在本地 SharePoint 场中下载、安装并配置 AzureCP。 

1. 在本地 SharePoint 场或替代的自定义声明提供程序解决方案中配置 AzureCP。 https://yvand.github.io/AzureCP/Register-App-In-AAD.html 上提供了 AzureCP 配置步骤

1. 在 Azure 门户中，打开 Azure AD 目录。 单击“企业应用程序”，单击前面创建的企业应用程序名称，然后单击“单一登录”。   

1. 在“设置 SAM 单一登录”页上，编辑“用户属性和声明”部分。  

1. 单击“添加组声明”。 

1. 选择哪些与用户关联的组应在声明中返回，在本例中，请选择“所有组”，然后在“源属性”部分选择“组 ID”并单击“保存”。   

若要向本地 SharePoint 中的 Azure Active Directory 安全组授予访问权限，需要共享网站集，或者将 Azure Active Directory 安全组添加到网站集的某个组。

1. 浏览到“SharePoint 网站集”，在“网站集”的“网站设置”下，单击“人员和组”。 选择 SharePoint 组，单击“新建”>“将用户添加到此组”，然后开始键入组的名称。此时，人员选取器将显示“Azure Active Directory 安全组”。

    ![将 Azure AD 组添加到 Sharepoint 组](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>在 Azure 门户中向来宾帐户授予对本地 SharePoint 的访问权限

现在，可以通过一致的方式向来宾帐户授予对 SharePoint 网站的访问权限。 执行此操作时，会修改 UPN。 具有 jdoe@outlook.com 的用户将以类似于 `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` 的形式表示。 与外部用户共享网站时可以获得无缝的体验。需要在 Azure 门户中的“用户属性和声明”部分添加一些修改内容。 

1. 在 Azure 门户中，打开 Azure AD 目录。 单击“企业应用程序”，单击前面创建的企业应用程序名称，然后单击“单一登录”。   

1. 在“设置 SAM 单一登录”页上，编辑“用户属性和声明”部分。  

1. 在“必需的声明”区域中，单击“唯一用户标识符(名称 ID)”。  

1. 将“源属性”属性更改为值 user.localuserprincipalname，然后单击“保存”。   

    ![“用户属性和声明”初始值](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. 使用功能区返回到“基于 SAML 的登录”，现在，“用户属性和声明”部分如下所示：   

    ![“用户属性和声明”最终值](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > 此设置中不需要姓氏和名字。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”。  

1. 单击“新建来宾用户” 

1. 选择选项“邀请用户”并填写用户的属性，然后单击“邀请”。  

1. 现在可以与 MyGuestAccount@outlook.com 共享网站，并允许此用户访问该网站。

    ![与来宾帐户共享网站](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>为多个 Web 应用程序配置受信任标识提供者

该配置适用于单个 Web 应用程序，但如果你打算对多个 Web 应用程序使用相同的受信任标识提供者，则需要其他配置。 例如，假设我们已扩展了一个 Web 应用程序以使用 URL `https://sales.contoso.com`，现在也想要向 `https://marketing.contoso.com` 进行用户身份验证。 为此，我们需要更新标识提供者以采用 WReply 参数并更新 Azure AD 中的应用程序注册以添加回复 URL。

1. 在 Azure 门户中，打开 Azure AD 目录。 单击“企业应用程序”，单击前面创建的企业应用程序名称，然后单击“单一登录”。   

2. 在“设置 SAM 单一登录”页中，编辑“基本 SAML 配置”。  

    ![编辑“基本 SAML 配置”](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. 在“回复 URL (断言使用者服务 URL)”中添加其他 Web 应用程序的 URL，然后单击“保存”。  

    ![编辑“基本 SAML 配置”](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. 在 SharePoint Server 上打开“SharePoint 201x Management Shell”，使用先前使用的受信任标识令牌颁发者的名称执行以下命令。 
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. 在管理中心内，转到该 Web 应用程序并启用现有的受信任标识提供者。

你可能还想在其他方案中为内部用户授予对本地 Sharepoint 的访问权限。对于此类方案，必须部署 Microsoft Azure Active Directory Connect，以便将本地用户同步到 Azure Active Directory。另有一篇文章介绍了此设置。 

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [什么是使用 Azure Active Directory 的混合标识？](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
