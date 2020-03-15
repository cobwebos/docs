---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SAP Fiori 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 SAP Fiori 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SAP Fiori 集成

本教程介绍如何将 SAP Fiori 与 Azure Active Directory (Azure AD) 集成。 将 SAP Fiori 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAP Fiori。
* 让用户使用其 Azure AD 帐户自动登录到 SAP Fiori。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SAP Fiori 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SAP Fiori 支持 **SP** 发起的 SSO

> [!NOTE]
> 对于 SAP Fiori 启动的 iFrame 身份验证，我们建议在 SAML AuthnRequest 中使用 IsPassive  参数进行无提示身份验证。 有关 IsPassive  参数的详细信息，请参阅 [Azure AD SAML 单一登录](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)信息

## <a name="adding-sap-fiori-from-the-gallery"></a>从库中添加 SAP Fiori

若要配置 SAP Fiori 与 Azure AD 的集成，需要从库中将 SAP Fiori 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SAP Fiori”   。
1. 从结果面板中选择“SAP Fiori”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>配置和测试 SAP Fiori 的 Azure AD 单一登录

使用名为 B.Simon  的测试用户配置并测试 SAP Fiori 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 SAP Fiori 相关用户之间建立链接关系。

若要配置和测试 SAP Fiori 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SAP Fiori SSO](#configure-sap-fiori-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SAP Fiori 测试用户](#create-sap-fiori-test-user)** - 在 SAP Fiori 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

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

1. 在 [Azure 门户](https://portal.azure.com/)的“SAP Fiori”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”窗格中    。 在“登录 URL”  框中，输入具有以下模式的 URL：`https:\//\<your company instance of SAP Fiori\>`。

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

    1. 在“参数 1”列表中，选择“user.userprincipalname”   。

    1. 选择“保存”。 

       ![“管理用户声明”窗格](./media/sapfiori-tutorial/nameidattribute.png)

       ![“管理用户声明”窗格中的“转换”部分](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 SAP Fiori”部分中，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 SAP Fiori 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“SAP Fiori”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-sap-fiori-sso"></a>配置 SAP Fiori 单一登录

1. 登录到 SAP 系统并转到事务代码 SAML2  。 将打开显示 SAML 配置页面的新浏览器窗口。

1. 要为受信任的标识提供程序 (Azure AD) 配置终结点，请选择“受信任的提供程序”选项卡  。

    ![SAP 中的“受信任的提供程序”选项卡](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. 选择“添加”，然后从上下文菜单选择“上传元数据文件”   。

    ![SAP 中的“添加和上传元数据文件”选项](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. 上传从 Azure 门户下载的元数据文件。 选择“**下一页**”。

    ![选择要在 SAP 中上传的元数据文件](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 在下一页的“别名”框中，输入别名  。 例如：aadsts  。 选择“**下一页**”。

    ![SAP 中的“别名”框](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. 确保“摘要算法”框中的值为 SHA-256   。 选择“**下一页**”。

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

### <a name="create-sap-fiori-test-user"></a>创建 SAP Fiori 测试用户

在本部分，我们将在 SAP Fiori 中创建名为 Britta Simon 的用户。 与内部 SAP 专家团队或组织 SAP 合作伙伴合作，以便在 SAP Fiori 平台中添加用户。

## <a name="test-sso"></a>测试 SSO

1. 在 SAP Fior 中激活身份提供程序 Azure AD 后，请尝试访问以下一个 URL 以测试单一登录（不应看到输入用户名和密码的提示）：

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > 将 sapurl 替换为实际的 SAP 主机名  。

1. 测试 URL 应将你转到 SAP 中的如下测试应用程序页面。 如果页面打开，则表明已成功设置 Azure AD 单一登录。

    ![SAP 中的标准测试应用程序页面](./media/sapfiori-tutorial/testingsso.png)

1. 如果系统提示输入用户名和密码，请启用跟踪以帮助诊断问题。 请使用以下 URL 进行跟踪：https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 SAP Fiori](https://aad.portal.azure.com/)
