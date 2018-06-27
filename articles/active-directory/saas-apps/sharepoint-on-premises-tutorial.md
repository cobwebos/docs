---
title: 教程：Azure Active Directory 与本地 SharePoint 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与本地 SharePoint 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 8cae1e2670ba449c0db28209c54b740e927bbb73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210371"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>教程：Azure Active Directory 与本地 SharePoint 的集成

本教程介绍如何将本地 SharePoint 与 Azure Active Directory (Azure AD) 集成。

将本地 SharePoint 与 Azure AD 集成可获得以下优势：

- 可在 Azure AD 中控制谁有权访问本地 SharePoint。
- 可让用户使用其 Azure AD 帐户自动登录到本地 SharePoint（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与本地 SharePoint 的集成，需要准备好以下各项：

- Azure AD 订阅
- 已启用本地 SharePoint 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加本地 SharePoint
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>从库中添加本地 SharePoint
若要配置本地 SharePoint 与 Azure AD 的集成，需要从库中将本地 SharePoint 添加到托管 SaaS 应用列表。

**若要从库中添加本地 SharePoint，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“本地 SharePoint”，在结果面板中选择“本地 SharePoint”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的“本地 SharePoint”](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试本地 SharePoint 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的本地 SharePoint 用户。 换句话说，需要在 Azure AD 用户与本地 SharePoint 中相关用户之间建立链接关系。

若要配置和测试本地 SharePoint 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建本地 SharePoint 测试用户](#create-a-sharePoint-on-premises-test-user)** - 在本地 SharePoint 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录，并在本地 SharePoint 应用程序中配置单一登录。

**若要配置本地 SharePoint 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“本地 SharePoint”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. 在“本地 SharePoint 域和 URL”部分执行以下步骤：

    ![本地 SharePoint 域和 URL 单一登录信息](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系[本地 SharePoint 客户端支持团队](https://support.office.com/)获取这些值。

4. 在“SAML 签名证书”部分单击“元数据 XML”，然后在计算机上以 .cer 扩展名保存元数据文件。 复制下载的元数据文件的完整路径，并将其粘贴到记事本中。

    ![证书下载链接](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. 在“本地 SharePoint 配置”部分，单击“配置本地 SharePoint”打开“配置登录”窗口。 从“快速参考”部分中复制“单一登录服务 URL”。

    ![本地 SharePoint 配置](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > 本地 SharePoint 应用程序使用 SAML 1.1 令牌，因此，Azure AD 预期 WS 联合身份验证请求来自 SharePoint 服务器；身份验证后，它会颁发 SAML 1.1 令牌。

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到本地 SharePoint 公司站点。

8. **在 SharePoint Server 2016 中配置新的受信任标识提供者**

    登录到 SharePoint Server 2016 服务器并打开 SharePoint 2016 Management Shell。 填写 Azure 门户中的 $realm、$wsfedurl 和 $filepath 值，并运行以下命令配置新的受信任标识提供者。

    > [!TIP]
    > 如果不熟悉 PowerShell 的用法，或想要详细了解 PowerShell 的工作原理，请参阅 [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps)。 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    接下来，遵循以下步骤为应用程序启用受信任的标识提供者：

    a. 在管理中心，导航到“管理 Web 应用程序”并选择要使用 Azure AD 保护的 Web 应用程序。

    b. 在功能区中单击“身份验证提供程序”，然后选择要使用的区域。

    c. 选择“受信任的标识提供者”，然后选择刚刚注册的名为 *AzureAD* 的标识提供者。

    d. 在登录页上的 URL 设置中，选择“自定义登录页”并提供“/_trust/”值。

    e. 单击“确定”。

    ![配置身份验证提供程序](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-sharepoint-on-premises-test-user"></a>创建本地 SharePoint 测试用户

1. 在管理中心，单击“应用程序管理”。

2. 在“应用程序管理”页上的“Web 应用程序”部分，单击“管理 Web 应用程序”。

3. 单击相应的 Web 应用程序，然后单击“用户策略”。

4. 在“Web 应用程序的策略”中，单击“添加用户”。

    ![按名称声明搜索用户](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. 在“添加用户”对话框中的“区域”内单击相应的区域，然后单击“下一步”。

6. 在“Web 应用程序的策略”对话框中的“选择用户”部分，单击“浏览”图标。

7. 在“查找”文本框中，键入目录中某个用户的登录名，然后单击“搜索”。 </br>示例：*demouser@blueskyabove.onmicrosoft.com*。

8. 在列表视图中的 AzureAD 标题下，选择名称属性，单击“添加”，然后单击“确定”关闭对话框。

9. 在“权限”中，单击“完全控制”。

    ![向声明用户授予完全控制权限](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. 依次单击“完成”、“确定”。

### <a name="fixing-people-picker"></a>固定人员选取器

现在，用户可以使用 Azure AD 中的标识登录到 SharePoint 2016，但仍有机会改进用户体验。 例如，搜索某个用户会在人员选取器中显示多个搜索结果。 声明映射中创建的 3 个声明类型各有一个搜索结果。 若要使用人员选取器选择某个用户，必须准确键入其用户名，并选择“名称”声明结果。

![声明搜索结果](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

系统不会验证要搜索的值，因此，可能会出现拼写错误，或者用户意外选择分配错误的声明类型，例如 **SurName** 声明。 这可能会导致用户无法成功访问资源。

为了帮助解决这种情况，名为 [AzureCP](https://yvand.github.io/AzureCP/) 的开源解决方案为 SharePoint 2016 提供自定义的声明提供程序。 它使用 Azure AD Graph 来解析进入并执行验证功能的用户。 详细了解 [AzureCP](https://yvand.github.io/AzureCP/)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问本地 SharePoint 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到本地 SharePoint，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“本地 SharePoint”。

    ![应用程序列表中的“SharePoint”链接](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“本地 SharePoint”磁贴时，应会自动登录到本地 SharePoint 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png

