---
title: 教程：Azure Active Directory 与 SAP NetWeaver 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP NetWeaver 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5a1034a55bc4d516244e11d035f17fcc3b94929
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855633"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>教程：Azure Active Directory 与 SAP NetWeaver 集成

在本教程中，了解如何将 SAP NetWeaver 与 Azure Active Directory (Azure AD) 集成。
将 SAP NetWeaver 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SAP NetWeaver。
* 可让用户使用其 Azure AD 帐户自动登录到 SAP NetWeaver（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP NetWeaver 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 SAP NetWeaver 单一登录的订阅
* 需要至少 SAP NetWeaver V7.20

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP NetWeaver 支持 **SP** 发起的 SSO

## <a name="adding-sap-netweaver-from-the-gallery"></a>从库中添加 SAP NetWeaver

要配置 SAP NetWeaver 与 Azure AD 的集成，需要从库中将 SAP NetWeaver 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP NetWeaver，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“SAP NetWeaver”，在结果面板中选择“SAP NetWeaver”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 SAP NetWeaver](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 SAP NetWeaver 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 SAP NetWeaver 中相关用户之间建立链接关系。

若要配置和测试 SAP NetWeaver 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP NetWeaver 单一登录](#configure-sap-netweaver-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP NetWeaver 测试用户](#create-sap-netweaver-test-user)** - 在 SAP NetWeaver 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SAP NetWeaver 的 Azure AD 单一登录，请执行以下步骤：

1. 打开新的 Web 浏览器窗口，以管理员身份登录 SAP NetWeaver 公司站点

2. 请确保“http”和“https”服务处于活动状态，并且在 SMICM“T-Code”中分配了相应的端口。

3. 登录到 SAP 系统 (T01) 的业务客户端（需要 SSO）并激活 HTTP 安全会话管理。

    a. 转到事务代码“SICF_SESSIONS”。 它显示具有当前值的所有相关配置文件参数。 这些参数如下所示：
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
4. 转到 SAP 系统 [T01/122] 的业务客户端中的事务代码“SAML2”。 它将在浏览器中打开用户界面。 在此示例中，我们假定 122 为 SAP 业务客户端。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. 提供用户名和密码以进入用户界面，然后单击“编辑”。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. 将“提供程序名称”从 T01122 替换为 `http://T01122`，然后单击“保存”。

    > [!NOTE]
    > 默认情况下，提供程序名称为 <sid> <client> 格式，但 Azure AD 需要格式为 <protocol>://<name> 的名称，建议将提供程序名称保留为 https://<sid><client> 以允许在 Azure AD 中配置多个 SAP NetWeaver ABAP 引擎。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **生成服务提供程序元数据**：完成在 SAML 2.0 用户界面上配置“本地提供商”和“受信任的提供程序”设置后，下一步将是生成服务提供程序元数据文件（包含 SAP 中的所有设置、身份验证上下文和其他配置）。 生成此文件后，需要在 Azure AD 中上传此文件。

    ![证书下载链接](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. 转到“本地提供程序”选项卡。

    b. 单击“元数据”。

    c. 将生成的“元数据 XML 文件”保存在计算机上，并将其上传到“基本 SAML 配置”部分，以便在 Azure 门户中自动填充“标识符”和“回复 URL”值。

8. 在 [Azure 门户](https://portal.azure.com/)中，在 **SAP NetWeaver** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

9. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

10. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

11. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 单击“上传元数据文件”以上传之前获取的“服务提供程序元数据文件”。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，**标识符**和**回复 URL** 值会自动填充在“基本 SAML 配置”部分的文本框中，如下所示：

    ![SAP NetWeaver 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    d. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<your company instance of SAP NetWeaver>`。

    > [!NOTE]
    > 我们发现很少有客户报告“为其实例配置了错误的回复 URL”错误。 如果你收到任何此类错误，可以使用以下 PowerShell 脚本作为解决方案为实例设置正确的回复 URL：
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal 对象 ID 将先由你自己设置，或者你也可以在此处传递该 ID。

12. SAP NetWeaver 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

13. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    a. 单击“编辑图标”，打开“管理用户声明”对话框。

    ![图像](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![图像](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. 从“转换”列表中，选择“ExtractMailPrefix()”。

    c. 从“参数 1”列表中，选择“user.userprinicipalname”。

    d. 单击“ **保存**”。

14. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

15. 在“设置 SAP NetWeaver”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-sap-netweaver-single-sign-on"></a>配置 SAP NetWeaver 单一登录

1. 登录 SAP 系统并转到事务代码 SAML2。 这将打开带有 SAML 配置屏幕的新浏览器窗口。

2. 要为受信任的标识提供程序 (Azure AD) 配置终结点，请转到“受信任的提供程序”选项卡。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. 按“添加”，然后从上下文菜单选择“上传元数据文件”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. 上传从 Azure 门户下载的元数据文件。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. 在下一个屏幕中，键入别名。 例如，键入 aadsts 并按“下一步”以继续操作。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. 请确保“摘要算法”应为“SHA-256”且无需进行任何更改，然后按“下一步”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. 在“单一登录终结点”上，使用“HTTP POST”并单击“下一步”以继续。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. 在“单一注销终结点”上，选择“HTTPRedirect”并单击“下一步”以继续。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. 在“项目终结点”上，按“下一步”以继续。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. 在“身份验证要求”上，单击“完成”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. 转到选项卡“受信任的提供程序” > “联合身份验证”（屏幕底部）。 单击“编辑”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. 单击“联合身份验证”（底部窗口）选项卡下的“添加”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. 从弹出窗口中选择“支持的 NameID 格式”中的“未指定”，然后单击“确定”。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. 请注意，“用户 ID 源”和“用户 ID 映射模式”值确定 SAP 用户与 Azure AD 声明之间的链接。  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>场景：SAP 用户到 Azure AD 用户的映射。

    a. 从 SAP 获取 NameID 详细信息的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. 提及 Azure AD 中的所需声明的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>场景：根据 SU01 中配置的电子邮件地址选择 SAP 用户 ID。 在这种情况下，应在 su01 中为每个需要 SSO 的用户配置电子邮件 ID。

    a.  从 SAP 获取 NameID 详细信息的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. 提及 Azure AD 中的所需声明的屏幕截图。

    ![配置单一登录](./media/sapnetweaver-tutorial/claimsaad2.png)

15. 单击“保存”，再单击“启用”以启用标识提供程序。

    ![配置单一登录](./media/sapnetweaver-tutorial/configuration1.png)

16. 出现提示后，请单击“确定”。

    ![配置单一登录](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP NetWeaver 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SAP NetWeaver”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SAP NetWeaver”。

    ![应用程序列表中的 SAP NetWeaver 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-sap-netweaver-test-user"></a>创建 SAP NetWeaver 测试用户

在本部分中，会在 SAP NetWeaver 中创建一个名为“Britta Simon”的用户。 请与内部 SAP 专家团队合作，或与组织 SAP 合作伙伴合作，以便在 SAP NetWeaver 平台中添加用户。

### <a name="test-single-sign-on"></a>测试单一登录 

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

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
