---
title: 教程：教程：Azure Active Directory 单一登录 (SSO) 与 SAP NetWeaver 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP NetWeaver 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897739"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SAP NetWeaver 的集成

本教程介绍如何将 SAP NetWeaver 与 Azure Active Directory (Azure AD) 集成。 将 SAP NetWeaver 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAP NetWeaver。
* 让用户使用其 Azure AD 帐户自动登录到 SAP NetWeaver。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SAP NetWeaver 单一登录 (SSO) 的订阅。
* 需要至少 SAP NetWeaver V7.20

## <a name="scenario-description"></a>方案描述

SAP NetWeaver 支持 **SAML**（**SP 发起的 SSO**）和 **OAuth**。 本教程在测试环境中配置并测试 Azure AD SSO。 

> [!NOTE]
> 根据组织的要求在 SAML 或 OAuth 中配置应用程序。 

## <a name="adding-sap-netweaver-from-the-gallery"></a>从库中添加 SAP NetWeaver

要配置 SAP NetWeaver 与 Azure AD 的集成，需要从库中将 SAP NetWeaver 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **SAP NetWeaver**。 
1. 在结果面板中选择“SAP NetWeaver”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>配置并测试 SAP NetWeaver 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 SAP NetWeaver 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SAP NetWeaver 中的相关用户之间建立链接关系。

若要配置并测试 SAP NetWeaver 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[使用 SAML 配置 SAP NetWeaver](#configure-sap-netweaver-using-saml)** ，在应用程序端配置 SSO 设置。
    1. **[创建 SAP NetWeaver 测试用户](#create-sap-netweaver-test-user)** ，在 SAP NetWeaver 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。
1. **[配置 SAP NetWeaver 的 OAuth](#configure-sap-netweaver-for-oauth)** ，在应用程序端配置 OAuth 设置。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SAP NetWeaver 的 Azure AD 单一登录，请执行以下步骤：

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 SAP NetWeaver 公司站点

1. 请确保“http”和“https”服务处于活动状态，并且在 SMICM“T-Code”中分配了相应的端口    。

1. 登录到 SAP 系统 (T01) 的业务客户端（需要 SSO）并激活 HTTP 安全会话管理。

    a. 转到事务代码“SICF_SESSIONS”  。 它显示具有当前值的所有相关配置文件参数。 这些参数如下所示：
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > 根据组织要求调整上述参数，以上参数仅作为指示给出。

    b. 如果需要调整参数，请在 SAP 系统的实例/默认配置文件中重启 SAP 系统。

    c. 双击相关客户端以启用 HTTP 安全会话。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 激活以下 SICF 服务：
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. 转到 SAP 系统 [T01/122] 的业务客户端中的事务代码“SAML2”  。 它将在浏览器中打开用户界面。 在此示例中，我们假定 122 为 SAP 业务客户端。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. 提供用户名和密码以进入用户界面，然后单击“编辑”  。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. 将“提供程序名称”从 T01122 替换为 `http://T01122`，然后单击“保存”   。

    > [!NOTE]
    > 默认情况下，提供程序名称采用 `<sid><client>` 格式，但 Azure AD 需要格式为 `<protocol>://<name>` 的名称，建议将提供程序名称保留为 `https://<sid><client>` 以允许在 Azure AD 中配置多个 SAP NetWeaver ABAP 引擎。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **生成服务提供程序元数据**：完成在 SAML 2.0 用户界面上配置“本地提供商”和“受信任的提供程序”设置后，下一步将是生成服务提供程序元数据文件（包含 SAP 中的所有设置、身份验证上下文和其他配置）   。 生成此文件后，需要在 Azure AD 中上传此文件。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. 转到“本地提供程序”选项卡  。

    b. 单击“元数据”  。

    c. 将生成的“元数据 XML 文件”保存在计算机上，并将其上传到“基本 SAML 配置”部分，以便在 Azure 门户中自动填充“标识符”和“回复 URL”值     。

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“SAP NetWeaver”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    a. 单击“上传元数据文件”以上传之前获取的“服务提供程序元数据文件”   。

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    c. 成功上传元数据文件后，**标识符**和**回复 URL** 值会自动填充在“基本 SAML 配置”  部分的文本框中，如下所示：

    d. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<your company instance of SAP NetWeaver>`。

    > [!NOTE]
    > 我们发现很少有客户报告“为其实例配置了错误的回复 URL”错误。 如果你收到任何此类错误，可以使用以下 PowerShell 脚本作为解决方案为实例设置正确的回复 URL：
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal 对象 ID 将先由你自己设置，或者你也可以在此处传递该 ID。

1. SAP NetWeaver 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    ![image](common/edit-attribute.png)

1. 在“用户属性”  对话框的“用户声明”  部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    a. 单击“编辑图标”，打开“管理用户声明”对话框   。

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. 从“转换”列表中，选择“ExtractMailPrefix()”   。

    c. 从“参数 1”列表中选择“user.userprincipalname”   。

    d. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 SAP NetWeaver”部分，根据要求复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
    1. 在“名称”  字段中，输入 `B.Simon`。  
    1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
    1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 SAP NetWeaver 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SAP NetWeaver”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

    ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-sap-netweaver-using-saml"></a>使用 SAML 配置 SAP NetWeaver

1. 登录到 SAP 系统并转到事务代码 SAML2。 这将打开带有 SAML 配置屏幕的新浏览器窗口。

2. 要为受信任的标识提供程序 (Azure AD) 配置终结点，请转到“受信任的提供程序”选项卡  。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. 按“添加”，然后从上下文菜单选择“上传元数据文件”   。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. 上传从 Azure 门户下载的元数据文件。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. 在下一个屏幕中，键入别名。 例如，键入 aadsts 并按“下一步”以继续操作  。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. 请确保“摘要算法”应为“SHA-256”且无需进行任何更改，然后按“下一步”    。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. 在“单一登录终结点”上，使用“HTTP POST”并单击“下一步”以继续    。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. 在“单一注销终结点”上，选择“HTTPRedirect”并单击“下一步”以继续    。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. 在“项目终结点”上，按“下一步”以继续   。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. 在“身份验证要求”上，单击“完成”   。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. 转到选项卡“受信任的提供程序” > “联合身份验证”（屏幕底部）   。 单击 **“编辑”** 。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. 单击“联合身份验证”（底部窗口）选项卡下的“添加”   。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. 从弹出窗口中选择“支持的 NameID 格式”中的“未指定”，然后单击“确定”   。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. 请注意，“用户 ID 源”和“用户 ID 映射模式”值确定 SAP 用户与 Azure AD 声明之间的链接   。  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>方案：SAP 用户到 Azure AD 用户的映射。

    a. 从 SAP 获取 NameID 详细信息的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. 提及 Azure AD 中的所需声明的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>方案：根据 SU01 中配置的电子邮件地址选择 SAP 用户 ID。 在这种情况下，应在 su01 中为每个需要 SSO 的用户配置电子邮件 ID。

    a.  从 SAP 获取 NameID 详细信息的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. 提及 Azure AD 中的所需声明的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/claimsaad2.png)

15. 单击“保存”，再单击“启用”以启用标识提供程序   。

    ![配置单一登录](./media/sapnetweaver-tutorial/configuration1.png)

16. 出现提示后，请单击“确定”  。

    ![配置单一登录](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>创建 SAP NetWeaver 测试用户

    在本部分，你将在 SAP NetWeaver 中创建名为 B.Simon 的用户。 请与内部 SAP 专家团队合作，或与组织 SAP 合作伙伴合作，以便在 SAP NetWeaver 平台中添加用户。

## <a name="test-sso"></a>测试 SSO

1. 激活 Azure AD 的标识提供程序后，请尝试访问以下 URL 以检查 SSO（不会提示输入用户名和密码）

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    （或者）使用以下 URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Sapurl 替换为实际的 SAP 主机名。

2. 单击上面的 URL 应转到下面所述的屏幕。 如果能够访问以下页面，则 Azure AD SSO 设置已成功完成。

    ![配置单一登录](./media/sapnetweaver-tutorial/testingsso.png)

3. 如果出现用户名和密码提示，请通过使用以下 URL 启用跟踪来诊断问题

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>配置 SAP NetWeaver 的 OAuth

1. 以下位置提供了 SAP 阐述的过程：[启用 NetWeaver 网关服务和创建 OAuth 2.0 范围](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. 转到“SPRO”并找到“激活和维护服务”。 

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth01.png)

3. 在本示例中，我们使用 OAuth 将 OData 服务 `DAAG_MNGGRP` 连接到 Azure AD SSO。 使用技术服务名称搜索服务 `DAAG_MNGGRP`，如果该服务尚未激活，请将其激活（查看“ICF 节点”选项卡下的 `green` 状态）。 确保系统别名（实际运行服务的已连接后端系统）正确。

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth02.png)

    * 然后单击顶部按钮栏上的“OAuth”按钮，并分配 `scope`（保留提供的默认名称）。 

4. 在本示例中，范围为 `DAAG_MNGGRP_001`，它是通过自动添加数字基于服务名称生成的。 报告 `/IWFND/R_OAUTH_SCOPES` 可用于更改范围名称或手动创建。

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > 消息 `soft state status is not supported` – 可以忽略，因为未出现问题。 有关更多详细信息，请参阅[此文](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>为 OAuth 2.0 客户端创建服务用户

1. OAuth2 使用 `service ID` 代表最终用户获取访问令牌。 OAuth 设计的重要限制：`OAuth 2.0 Client ID` 必须与 OAuth 2.0 客户端在请求访问令牌时用于登录的 `username` 相同。 因此，对于本示例，我们将注册名为 CLIENT1 的 OAuth 2.0 客户端，作为先决条件，SAP 系统中必须存在同名 (CLIENT1) 的用户，并且我们要将该用户配置为由被引用的应用程序使用。 

2. 注册 OAuth 客户端时，我们将使用 `SAML Bearer Grant type`。

    >[!NOTE]
    >有关更多详细信息，请参阅 [SAML 持有者授权类型的 OAuth 2.0 客户端注册](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod:SU01 / 创建 `System type` 的用户 CLIENT1 并分配密码，根据需要保存设置，以便为 API 程序员提供凭据。程序员应将凭据和用户名一起添加到调用代码。 不要分配配置文件或角色。

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>使用创建向导注册新的 OAuth 2.0 客户端 ID

1. 若要注册新的 **OAuth 2.0 客户端**，请启动事务 **SOAUTH2**。 该事务将显示有关已注册的 OAuth 2.0 客户端的概述。 选择“创建”，针对本示例中名为 CLIENT1 的新 OAuth 客户端启动向导。 

2. 转到“T-Code:SOAUTH2”并提供说明，然后单击“下一步”。  

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth04.png)

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth05.png)

3. 从下拉列表中选择已添加的“SAML2 IdP – Azure AD”，然后单击“保存”。 

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth06.png)

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth07.png)

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth08.png)

4. 单击范围分配下的“添加”，以添加前面创建的范围：  `DAAG_MNGGRP_001`

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth09.png)

    ![配置单一登录](./media/sapnetweaver-tutorial/oauth10.png)

5. 单击“完成”。 

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 SAP NetWeaver](https://aad.portal.azure.com/)