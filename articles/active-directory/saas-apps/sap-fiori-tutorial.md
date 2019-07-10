---
title: 教程：Azure Active Directory 与 SAP Fiori 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 SAP Fiori 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092154"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>教程：Azure Active Directory 与 SAP Fiori 的集成

本教程介绍如何将 SAP Fiori 与 Azure Active Directory (Azure AD) 集成。

将 SAP Fiori 与 Azure AD 集成提供以下优势：

* 可以使用 Azure AD 控制有权限访问 SAP Fiori 的人员。
* 用户可使用其 Azure AD 帐户（单一登录）自动登录到 SAP Fiori。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Fiori 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 SAP Fiori 订阅。
* 需要 SAP Fiori 7.20 或更高版本。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 SAP Fiori 与 Azure AD 集成。

SAP Fiori 支持以下功能：

* **SP 发起的单一登录**

## <a name="add-sap-fiori-in-the-azure-portal"></a>在 Azure 门户中添加 SAP Fiori

若要将 SAP Fiori 与 Azure AD 集成，必须将 SAP Fiori 添加到托管 SaaS 应用列表中。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加应用程序，请选择“新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“SAP Fiori”  。 在搜索结果中选择“SAP Fiori”，并选择“添加”   。

    ![结果列表中的“SAP Fiori”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 SAP Fiori 的 Azure AD 单一登录。 要正常使用单一登录，需要在 Azure AD 用户与 SAP Fiori 相关用户之间建立链接关系。

若要配置并测试 SAP Fiori 的 Azure AD 单一登录，必须完成以下构建基块：

| 任务 | 说明 |
| --- | --- |
| **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** | 使用户能够使用此功能。 |
| **[配置 SAP Fiori 单一登录](#configure-sap-fiori-single-sign-on)** | 在应用程序中配置单一登录设置。 |
| **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** | 测试名为 Britta Simon 的用户的 Azure AD 单一登录。 |
| **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** | 使 Britta Simon 能够使用 Azure AD 单一登录。 |
| **[创建 SAP Fiori 测试用户](#create-an-sap-fiori-test-user)** | 在 SAP Fiori 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。 |
| **[测试单一登录](#test-single-sign-on)** | 验证配置是否正常工作。 |

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中使用 SAP Fiori 配置 Azure AD 单一登录。

1. 打开新的 Web 浏览器窗口，以管理员身份登录 SAP Fiori 公司站点。

1. 请确保“http”和“https”服务处于活动状态，并且已对事务代码 SMICM 分配相关端口    。

1. 登录适用于 SAP 系统 T01 的 SAP Business Client（需要单一登录）  。 然后，激活 HTTP 安全会话管理。

    1. 转到事务代码“SICF_SESSIONS”  。 所有具有当前值的相关配置文件参数都将显示。 这些参数应与以下示例类似：

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
        > 根据组织要求调整参数。 上述参数仅作为示例。

    1. 如有必要，请在 SAP 系统的实例（默认）配置文件中调整参数并重启 SAP 系统。

    1. 双击相关客户端以启用 HTTP 安全会话。

        ![SAP 中的“相关配置文件参数的当前值”页面](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. 激活以下 SICF 服务：

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. 转到 SAP 系统 [T01/122] 的 Business Client 中的事务代码“SAML2”   。 配置 UI 将在新浏览器窗口中打开。 在本示例中，我们使用适用于 SAP 系统 122 的 Business Client。

    ![SAP Fiori Business Client 登录页面](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. 输入用户名和密码，然后选择“登录”  。

    ![SAP 中的“ABAP 系统 T01/122 的 SAML 2.0 配置”页面](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. 在“提供程序名称”框中，将“T01122”替换为“http:\//T01122”，然后选择“保存”     。

    > [!NOTE]
    > 默认情况下，提供程序名称的格式为 \<sid>\<client>。 Azure AD 要求的名称格式为 \<protocol>://\<name>。 我们建议将提供程序名称的格式保留为 https\://\<sid>\<client>，以便可在 Azure AD 中配置多个 SAP Fiori ABAP 引擎。

    ![SAP 中的“ABAP 系统 T01/122 的 SAML 2.0 配置中的已更新提供程序名称”页面](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. 选择“本地提供程序”选项卡 > “元数据”   。

1. 在“SAML 2.0 元数据”对话框中，下载生成的元数据 XML 文件并将其保存在计算机上  。

    ![“SAP SAML 2.0 元数据”对话框中的“下载元数据”链接](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. 在 [Azure 门户](https://portal.azure.com/)中的“SAP Fiori”应用程序集成窗格中，选择“单一登录”   。

    ![“单一登录”选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML”或“SAML/WS-Fed”模式以启用单一登录    。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）以打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分完成以下步骤： 

    1. 选择“上传元数据文件”  。

        ![“上传元数据文件”选项](common/upload-metadata.png)

   1. 要选择元数据文件，请选择文件夹图标，然后选择“上传”  。

       ![选择元数据文件，然后选择“上传”按钮](common/browse-upload-metadata.png)

1. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”窗格中    。 在“登录 URL”框中，输入具有以下模式的 URL：https:\//\<公司的 SAP Fiori 实例\>  。

    ![SAP Fiori 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    > [!NOTE]
    > 有一些客户报告了与“回复 URL”值配置错误相关的错误  。 如果遇到此错误，可以使用以下 PowerShell 脚本为实例设置正确的回复 URL：
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > 可以在运行脚本之前自己设置 `ServicePrincipal` 对象 ID，也可以在此处传递。

1. SAP Fiori 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 要管理这些属性值，请在“设置 SAML 单一登录”窗格中，选择“编辑”   。

    ![“用户属性”窗格](common/edit-attribute.png)

1. 在“用户属性和声明”窗格中，配置 SAML 令牌属性，如上图所示  。 然后完成以下步骤：

    1. 选择“编辑”以打开“管理用户声明”窗格   。

    1. 在“转换”列表中，选择“ExtractMailPrefix()”   。

    1. 在“参数 1”列表中，选择“user.userprinicipalname”   。

    1. 选择“保存”。 

       ![“管理用户声明”窗格](./media/sapfiori-tutorial/nameidattribute.png)

       ![“管理用户声明”窗格中的“转换”部分](./media/sapfiori-tutorial/nameidattribute1.png)


1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，选择“联合元数据 XML”旁边的“下载”     。 根据需要选择下载选项。 将证书保存在计算机上。

    ![证书下载选项](common/metadataxml.png)

1. 在“设置 SAP Fiori”部分，根据需要复制以下 URL  ：

    * 登录 URL
    * Azure AD 标识符
    * 注销 URL

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>配置 SAP Fiori 单一登录

1. 登录到 SAP 系统并转到事务代码 SAML2  。 将打开显示 SAML 配置页面的新浏览器窗口。

1. 要为受信任的标识提供程序 (Azure AD) 配置终结点，请选择“受信任的提供程序”选项卡  。

    ![SAP 中的“受信任的提供程序”选项卡](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. 选择“添加”，然后从上下文菜单选择“上传元数据文件”   。

    ![SAP 中的“添加和上传元数据文件”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. 上传从 Azure 门户下载的元数据文件。 选择“**下一步**”。

    ![选择要在 SAP 中上传的元数据文件](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 在下一页的“别名”框中，输入别名  。 例如：aadsts  。 选择“**下一步**”。

    ![SAP 中的“别名”框](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. 确保“摘要算法”框中的值为 SHA-256   。 选择“**下一步**”。

    ![验证 SAP 中的摘要算法值](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. 在“单一登录终结点”下，选择“HTTP POST”，然后选择“下一步”    。

    ![SAP 中的“单一登录终结点”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. 在“单一注销终结点”下，选择“HTTP 重定向”，然后选择“下一步”    。

    ![SAP 中的“单一注销终结点”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. 在“项目终结点”下，选择“下一步”以继续   。

    ![SAP 中的“项目终结点”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. 在“身份验证要求”下，选择“完成”   。

    ![SAP 中的“身份验证要求”选项和“完成”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. 选择“受信任的提供程序” > “联合身份验证”（页面底部）   。 选择“编辑”  。

    ![SAP 中的“受信任的提供程序”和“联合身份验证”选项卡](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. 选择 **添加** 。

    ![“联合身份验证”选项卡上的“添加”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. 在“支持的 NameID 格式”对话框中，选择“未指定”   。 选择“确定”  。

    ![SAP 中“支持的 NameID 格式”对话框和选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    “用户 ID 源”和“用户 ID 映射模式”的值确定 SAP 用户与 Azure AD 声明之间的链接   。  

    **场景 1**：SAP 用户到 Azure AD 用户的映射

    1. 在 SAP 中的“NameID 格式的详细信息 “未指定””下，请注意以下详细信息  ：

        ![SAP 中的“NameID 格式的详细信息 “未指定””对话框](./media/sapfiori-tutorial/nameiddetails.png)

    1. 在 Azure 门户中的“用户属性和声明”下，请注意 Azure AD 所需的声明  。

        ![Azure 门户中的“用户属性和声明”对话框](./media/sapfiori-tutorial/claimsaad1.png)

    **场景 2**：根据 SU01 中配置的电子邮件地址选择 SAP 用户 ID。 在这种情况下，应在 SU01 中为每个需要 SSO 的用户配置电子邮件 ID。

    1.  在 SAP 中的“NameID 格式的详细信息 “未指定””下，请注意以下详细信息  ：

        ![SAP 中的“NameID 格式的详细信息 “未指定””对话框](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. 在 Azure 门户中的“用户属性和声明”下，请注意 Azure AD 所需的声明  。

       ![Azure 门户中的“用户属性和声明”对话框](./media/sapfiori-tutorial/claimsaad2.png)

1. 选择“保存”，再选择“启用”以启用标识提供程序   。

    ![SAP 中的“保存”和“启用”选项](./media/sapfiori-tutorial/configuration1.png)

1. 出现提示时选择“确定”  。

    ![SAP 中“SAML 2.0 配置”对话框中的“确定”选项](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 选择“新建用户”。 

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中完成以下步骤： 

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入“brittasimon\@\<your-company-domain>.\<extension>”   。 例如，brittasimon\@contoso.com  。

    1. 选中“显示密码”复选框  。 记下“密码”框中显示的值  。

    1. 选择“创建”  。

    ![“用户”窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Fiori 的权限，使其可使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序” > “所有应用程序” > “SAP Fiori”    。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在“应用程序”列表中，选择“SAP Fiori”  。

    ![“应用程序”列表中的“SAP Fiori”链接](common/all-applications.png)

1. 在菜单中选择“用户和组”  。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”窗格中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，在用户列表中选择“Britta Simon”   。 选择“选择”。 

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相关的角色  。 选择“选择”。 

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-an-sap-fiori-test-user"></a>创建 SAP Fiori 测试用户

在本部分，我们将在 SAP Fiori 中创建名为 Britta Simon 的用户。 与内部 SAP 专家团队或组织 SAP 合作伙伴合作，以便在 SAP Fiori 平台中添加用户。

### <a name="test-single-sign-on"></a>测试单一登录

1. 在 SAP Fior 中激活身份提供程序 Azure AD 后，请尝试访问以下一个 URL 以测试单一登录（不应看到输入用户名和密码的提示）：

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > 将 sapurl 替换为实际的 SAP 主机名  。

1. 测试 URL 应将你转到 SAP 中的如下测试应用程序页面。 如果页面打开，则表明已成功设置 Azure AD 单一登录。

    ![SAP 中的标准测试应用程序页面](./media/sapfiori-tutorial/testingsso.png)

1. 如果系统提示输入用户名和密码，请启用跟踪以帮助诊断问题。 请使用以下 URL 进行跟踪：https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [用于将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
