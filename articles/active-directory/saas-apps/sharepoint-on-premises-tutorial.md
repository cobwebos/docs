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
ms.openlocfilehash: c623ed8cfcdb61081c74a0f7326106c42781cefa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648485"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>教程：Azure Active Directory 单一登录与本地 SharePoint 的集成

本教程介绍如何将本地 SharePoint 与 Azure Active Directory (Azure AD) 集成。 将本地 SharePoint 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问本地 SharePoint。
* 让用户使用其 Azure AD 帐户自动登录到本地 SharePoint。
* 在 Azure 门户中管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与本地 SharePoint 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 SharePoint 2013 场或更高版本的场。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录 (SSO)。 Azure AD 中的用户可以访问你的本地 SharePoint。

## <a name="create-enterprise-applications-in-the-azure-portal"></a>在 Azure 门户中创建企业应用程序

若要配置本地 SharePoint 与 Azure AD 的集成，需要从库中将本地 SharePoint 添加到托管 SaaS 应用列表。

若要从库中添加本地 SharePoint，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)最左侧的窗格中，选择“Azure Active Directory”。

   > [!NOTE]
   > 如果该元素不可用，还可通过最左侧窗格顶部的“所有服务”链接将其打开。 在以下概述中，“Azure Active Directory”链接位于“标识”部分。 还可以使用筛选器框来搜索它。

1. 转到“企业应用程序”，并选择“所有应用程序”。 

1. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

1. 在搜索框中，输入“SharePoint on-premises”。 从结果窗格中选择“SharePoint on-premises”。

    <kbd>![结果列表中的“本地 SharePoint”](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. 指定本地 SharePoint 实例的名称，然后选择“添加”以添加应用程序。

1. 在新企业应用程序中选择“属性”，然后检查“需要进行用户分配?”的值。

   <kbd>![“需要进行用户分配?”切换开关](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   在此方案中，值设置为“否”。

## <a name="configure-and-test-azure-ad"></a>配置和测试 Azure AD

在本部分，你将配置本地 SharePoint 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与本地 SharePoint 中的相关用户之间建立关联。

若要配置并测试本地 SharePoint 的 Azure AD SSO，请完成以下构建基块：

- [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
- [配置本地 SharePoint](#configure-sharepoint-on-premises) - 在应用程序端配置 SSO 设置。
- [在 Azure 门户中创建 Azure AD 测试用户](#create-an-azure-ad-test-user-in-the-azure-portal) - 在 Azure AD 中创建用于 SSO 的新用户。
- [在 Azure 门户中创建 Azure AD 安全组](#create-an-azure-ad-security-group-in-the-azure-portal) - 在 Azure AD 中创建用于 SSO 的新安全组。
- [向本地 SharePoint 中的 Azure AD 帐户授予权限](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) - 向 Azure AD 用户授予权限。
- [向本地 SharePoint 中的 Azure AD 组授予权限](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) - 向 Azure AD 组授予权限。
- [在 Azure 门户中向来宾帐户授予对本地 SharePoint 的访问权限](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) - 向 Azure AD 中的来宾帐户授予对本地 SharePoint 的权限。
- [为多个 Web 应用程序配置受信任的标识提供者](#configure-the-trusted-identity-provider-for-multiple-web-applications) - 对多个 Web 应用程序使用相同的受信任标识提供者。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，你将在 Azure 门户中启用 Azure AD SSO。

若要配置本地 SharePoint 的 Azure AD SSO，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“Azure Active Directory” > “企业应用程序”。 选择之前创建的企业应用程序名称，然后选择“单一登录”。

1. 在“选择单一登录方法”对话框中，选择“SAML”模式以启用 SSO 。
 
1. 在“使用 SAML 设置单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框。  

1. 在“基本 SAML 配置”部分执行以下步骤：

    ![本地 SharePoint 域和 URL SSO 信息](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. 在“标识符”框中，使用以下模式输入 URL：`urn:<sharepointFarmName>:<federationName>`。

    1. 在“回复 URL”框中，使用以下模式输入 URL：`https://<YourSharePointSiteURL>/_trust/`。

    1. 在“登录 URL”框中，使用以下模式输入 URL：`https://<YourSharePointSiteURL>/`。
    1. 选择“保存”。

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，选择“下载”以根据要求从给定的选项下载“证书(Base64)”并将其保存在计算机上。

    ![证书下载链接](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. 在“设置本地 SharePoint”部分，根据要求复制相应 URL：
    
    - **登录 URL**
    
        复制登录 URL，并将末尾的 /saml2 替换为 /wsfed，使其类似于 https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed 。 （此 URL 不准确。）

    - **Azure AD 标识符**
    - **注销 URL**

    > [!NOTE]
    > 此 URL 不能在 SharePoint 中按原样使用。 必须将 /saml2 替换为 /wsfed。 本地 SharePoint 应用程序使用 SAML 1.1 令牌，因此，Azure AD 预期 WS 联合身份验证请求来自 SharePoint 服务器。 验证身份后，它会颁发 SAML 1.1 令牌。

### <a name="configure-sharepoint-on-premises"></a>配置本地 SharePoint

1. 在 SharePoint Server 2016 中创建新的受信任标识提供者。

    登录到 SharePoint 服务器并打开 SharePoint Management Shell。 填写以下值：
    - $realm 是 Azure 门户中“本地 SharePoint 域和 URL”部分中的“标识符”值。
    - $wsfedurl 是 SSO 服务 URL。
    - $filepath 是从 Azure 门户将证书文件下载到其中的文件路径。

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
1. 为应用程序启用受信任的标识提供者。

    1. 在管理中心，转到“管理 Web 应用程序”并选择要使用 Azure AD 保护的 Web 应用程序。

    1. 在功能区中选择“身份验证提供程序”，然后选择要使用的区域。

    1. 选择“受信任的标识提供者”，然后选择刚才注册的名为“AzureAD”的标识提供者。

    1. 选择“确定”。

    ![配置身份验证提供程序](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建测试用户。

1. 在 Azure 门户最左侧的窗格中，选择“Azure Active Directory”。 在“管理”窗格中选择“用户” 。

1. 选择屏幕顶部的“所有用户” > “新建用户”。

1. 选择“创建用户”，然后在用户属性中执行以下步骤。 可能可以使用租户后缀或任何已验证的域在 Azure AD 中创建用户。 

    1. 在“名称”框中输入用户名。 我们使用“TestUser”。
  
    1. 在“用户名”框中输入 `TestUser@yourcompanydomain.extension`。 本示例显示 `TestUser@contoso.com`。

       ![“用户”对话框](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. 选中“显示密码”复选框，并记下“密码”框中显示的值 。

    1. 选择“创建”。

    1. 现在可以与 TestUser@contoso.com 共享网站，并允许此用户访问该网站。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 安全组

1. 选择“Azure Active Directory” > “组”。

1. 选择“新建组”。

1. 填写“组类型”、“组名称”、“组描述”和“成员身份类型”框   。 选择箭头以选择成员，然后搜索或选择要添加到组中的成员。 选择“选择”以添加选定的成员，然后选择“创建”。

![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>向本地 SharePoint 中的 Azure AD 帐户授予权限

若要向本地 SharePoint 中的 Azure AD 用户授予访问权限，请共享网站集或将 Azure AD 用户添加到网站集的一个组中。 现在，用户可以使用 Azure AD 中的标识登录到 SharePoint 201x，但仍有机会改善用户体验。 例如，搜索某个用户会在人员选取器中显示多个搜索结果。 在声明映射中创建的每个声明类型都有一个搜索结果。 若要使用人员选取器选择某个用户，必须准确输入其用户名，并选择“名称”声明结果。

![声明搜索结果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

系统不会验证要搜索的值，因此，可能会出现拼写错误，或者用户意外选择错误声明类型的情况。 这种情况可能导致用户无法成功访问资源。

为了修复此人员选取器问题，名为 [AzureCP](https://yvand.github.io/AzureCP/) 的开源解决方案为 SharePoint 2013、2016 和 2019 提供自定义的声明提供程序。 它使用 Microsoft Graph API 来解析用户输入的内容并执行验证。 有关详细信息，请参阅 [AzureCP](https://yvand.github.io/AzureCP/)。

  > [!NOTE]
  > 如果没有 AzureCP，可以通过添加 Azure AD 组的 ID 来添加组，但此方法不具备用户友好性，且不可靠。 其如下所示：
  > 
  >![将 Azure AD 组添加到 SharePoint 组](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>向本地 SharePoint 中的 Azure AD 组授予权限

若要将 Azure AD 安全组分配给本地 SharePoint，必须使用适用于 SharePoint 服务器的自定义声明提供程序。 本示例使用 AzureCP。

 > [!NOTE]
 > AzureCP 不是 Microsoft 产品，并且不受 Microsoft 支持部门的支持。 若要在本地 SharePoint 场中下载、安装并配置 AzureCP，请参阅 [AzureCP](https://yvand.github.io/AzureCP/) 网站。 

1. 在本地 SharePoint 场或替代自定义声明提供程序解决方案中配置 AzureCP。 若要配置 AzureCP，请访问此 [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) 网站。

1. 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。 选择之前创建的企业应用程序名称，然后选择“单一登录”。

1. 在“设置 SAML 单一登录”页上，编辑“用户属性和声明”部分 。

1. 选择“添加组声明”。

1. 选择声明中应返回的与用户关联的组。 在本例中，选择“所有组”。 在“源属性”部分，选择“组 ID”，然后选择“保存”。

若要向本地 SharePoint 中的 Azure AD 安全组授予访问权限，请共享网站集或将 Azure AD 安全组添加到网站集的一个组中。

1. 浏览到“SharePoint 网站集”。 在网站集的“网站设置”下，选择“人员和组”。 

1. 选择 SharePoint 组，然后选择“新建” > “将用户添加到此组”。 键入组的名称时，人员选择器会显示 Azure AD 安全组。

    ![将 Azure AD 组添加到 SharePoint 组](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>在 Azure 门户中向来宾帐户授予对本地 SharePoint 的访问权限

可通过一致的方式向来宾帐户授予对 SharePoint 网站的访问权限，因为 UPN 现已修改。 例如，用户 `jdoe@outlook.com` 表示为 `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`。 若要与外部用户共享网站，需要在 Azure 门户的“用户属性和声明”部分中添加一些修改。

1. 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。 选择之前创建的企业应用程序名称，然后选择“单一登录”。

1. 在“设置 SAML 单一登录”页上，编辑“用户属性和声明”部分 。

1. 在“必需声明”区域中，选择“唯一用户标识符(名称 ID)” 。

1. 将“源属性”属性更改为值“user.localuserprincipalname”，然后选择“保存”  。

    ![“用户属性和声明”初始值](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. 使用功能区返回“基于 SAML 的登录”。 现在，“用户属性和声明”部分如下所示： 

    ![“用户属性和声明”最终值](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > 此设置中不需要姓氏和名字。

1. 在 Azure 门户最左侧的窗格中，选择“Azure Active Directory”，然后选择“用户”。

1. 选择“新来宾用户”。

1. 选择“邀请用户”选项。 填写用户属性，然后选择“邀请”。

1. 现在可以与 MyGuestAccount@outlook.com 共享网站，并允许此用户访问该网站。

    ![与来宾帐户共享网站](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>为多个 Web 应用程序配置受信任标识提供者

该配置适用于单个 Web 应用程序，但如果你打算对多个 Web 应用程序使用相同的受信任标识提供者，则需要其他配置。 例如，假设已扩展了一个 Web 应用程序以使用 URL `https://sales.contoso.com`，且现在想要向 `https://marketing.contoso.com` 进行用户身份验证。 为此，请更新标识提供者以采用 WReply 参数，并更新 Azure AD 中的应用程序注册以添加回复 URL。

1. 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。 选择之前创建的企业应用程序名称，然后选择“单一登录”。

1. 在“设置 SAML 单一登录”页中，编辑“基本 SAML 配置” 。

    ![基本 SAML 配置](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. 对于“回复 URL (断言使用者服务 URL)”，请添加其他 Web 应用程序的 URL，然后选择“保存” 。

    ![编辑基本 SAML 配置](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. 在 SharePoint 服务器上，打开 SharePoint 201x Management Shell 并运行以下命令。 使用之前使用的受信任身份验证令牌颁发者的名称。
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. 在管理中心，转到 Web 应用程序并启用现有的受信任标识提供者。

你可能会遇到想要授予内部用户对本地 SharePoint 实例的访问权限的其他方案。 对于这种情况，你必须部署 Microsoft Azure Active Directory Connect 才能允许本地用户与 Azure AD 同步。 此设置将在另一篇文章中讨论。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [什么是使用 Azure Active Directory 的混合标识？](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
