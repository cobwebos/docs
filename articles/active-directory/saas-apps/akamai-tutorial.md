---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Akamai 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Akamai 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: fbec82e25424fd5220aa992cf2dd0e8449e6a0a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Akamai 的集成

本教程介绍如何将 Akamai 与 Azure Active Directory (Azure AD) 集成。 将 Akamai 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Akamai。
* 让用户使用其 Azure AD 帐户自动登录到 Akamai。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

Azure Active Directory 和 Akamai Enterprise Application Access 相集成，让你能够无缝地访问云中或本地托管的旧版应用程序。 集成解决方案利用 Azure Active Directory 的所有新式功能，例如 [Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)、[Azure AD 标识保护](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 和 [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)，不修改应用或安装代理就可访问旧版应用程序。

下图描述了 Akamai EAA 在哪些方面适合更广泛的混合安全访问方案

![Akamai EAA 适合更广泛的混合安全访问方案](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>密钥身份验证方案

除了对新式验证协议（如 Open ID Connect、SAML 和 WS-Fed）的 Azure Active Directory 原生集成支持以外，Akamai EAA 还为基于传统身份验证的应用扩展了安全访问，使其可采用 Azure AD 进行安全的内部和外部访问，从而让这些应用程序可实现无密码访问等新式方案。 这包括：

* 基于标头的身份验证应用
* 远程桌面
* SSH（安全外壳）
* Kerberos 身份验证应用
* VNC（虚拟网络计算）
* 匿名身份验证或非内置身份验证应用
* NTLM 身份验证应用（通过针对用户的双重提示进行保护）
* 基于窗体的应用程序（通过针对用户的双重提示进行保护）

### <a name="integration-scenarios"></a>集成方案

借助 Microsoft 与 Akamai EAA 的合作关系，可根据你的业务要求支持多个集成方案，灵活地满足业务要求。 这些方案可用于跨所有应用程序提供零时差覆盖，并逐步分类和配置适当的策略类别。

#### <a name="integration-scenario-1"></a>集成方案 1

Akamai EAA 被配置为 Azure AD 上的单个应用程序。 管理员可在应用程序上配置 CA 策略；一旦满足条件，用户就可获取对 Akamai EAA 门户的访问权限。

优点：

* 只需配置 IDP 一次

缺点：

* 用户最终拥有两个应用程序门户

* 单个通用 CA 策略覆盖所有应用程序。

![集成方案 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>集成方案 2

Akamai EAA 应用程序在 Azure AD 门户上单独设置。 管理员可在应用程序上配置单独的 CA 策略；一旦满足条件，用户就可直接重定向到特定的应用程序。

优点：

* 可定义单独的 CA 策略

* 所有应用都在 0365 Waffle 和 myApps.microsoft.com 面板上呈现。


缺点：

* 需要配置多个 IDP。

![集成方案 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Akamai 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

- Akamai 支持 IDP 发起的 SSO

#### <a name="important"></a>重要说明

下面列出的所有设置对于集成方案 1 和方案 2 都相同 。 对于集成方案 2，你需要在 Akamai EAA 中设置单独的 IDP，并且需要修改 URL 属性，使其指向应用程序 URL。

![重要说明](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>从库中添加 Akamai

若要配置 Akamai 与 Azure AD 的集成，需要从库中将 Akamai 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 **Akamai**。
1. 在结果面板中选择“Akamai”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>配置并测试 Akamai 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Akamai 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Akamai 中的相关用户之间建立链接关系。

若要配置并测试 Akamai 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Akamai SSO](#configure-akamai-sso)** - 在应用程序端配置单一登录设置。
    * **[设置 IDP](#setting-up-idp)**
    * **[基于标头的身份验证](#header-based-authentication)**
    * **[远程桌面](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos 身份验证](#kerberos-authentication)**
    * **[创建 Akamai 测试用户](#create-akamai-test-user)** - 在 Akamai 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Akamai”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Akamai 客户端支持团队](https://www.akamai.com/us/en/contact-us/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Akamai”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Akamai 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中选择“Akamai”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-akamai-sso"></a>配置 Akamai SSO

### <a name="setting-up-idp"></a>设置 IDP

**AKAMAI EAA IDP 配置**

1. 登录 Akamai Enterprise Application Access 控制台。
1. 在 Akamai EAA 控制台上，选择“标识” > “标识提供者”，然后单击“添加标识提供者”   。

    ![配置 Akamai](./media/header-akamai-tutorial/configure01.png)

1. 在“新建标识提供者”中执行以下步骤：

    ![配置 Akamai](./media/header-akamai-tutorial/configure02.png)

    a. 指定“唯一名称”。

    b. 选择“第三方 SAML”并单击“创建标识提供者并进行配置”。 

### <a name="general-settings"></a>常规设置

1. **标识截获** - 指定 SP 基 URL 的名称（将用于 Azure AD 配置）

    > [!NOTE]
    > 可以选择使用自己的自定义域（需要 DNS 条目和证书）。 本示例将使用 Akamai 域。

1. **Akamai 云区域** - 选择适当的云区域。
1. **证书验证** - 查看 Akamai 文档（可选）

    ![配置 Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>身份验证配置

1. URL – 指定与标识截获相同的 URL（身份验证后用户将重定向到此 URL）。
2. 注销 URL：更新注销 URL。
3. 签名 SAML 请求：默认未选中。
4. 对于“IDP 元数据文件”，请添加 Azure AD 控制台中的应用程序。

    ![配置 Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>会话设置

将设置保留为默认值。

![配置 Akamai](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>目录

跳过目录配置。

![配置 Akamai](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>自定义 UI

可将自定义添加到 IDP。

![配置 Akamai](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>高级设置

跳过高级设置；有关更多详细信息，请参阅 Akamai 文档。

![配置 Akamai](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>部署

1. 单击“部署标识提供者”。

    ![配置 Akamai](./media/header-akamai-tutorial/deployment.png)

2. 验证部署是否成功

### <a name="header-based-authentication"></a>基于标头的身份验证

基于 Akamai 标头的身份验证

1. 从“添加应用程序向导”中选择“自定义 HTTP”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure05.png)

2. 输入应用程序名称和描述 。

    ![配置 Akamai](./media/header-akamai-tutorial/configure06.png)

    ![配置 Akamai](./media/header-akamai-tutorial/configure07.png)

    ![配置 Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>身份验证

1. 选择“身份验证”选项卡。

    ![配置 Akamai](./media/header-akamai-tutorial/configure09.png)

2. 分配标识提供者

    ![配置 Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>服务

单击“保存并转到‘身份验证’”。

![配置 Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>高级设置

1. 在“客户 HTTP 标头”下，指定“客户标头”和“SAML 属性”。  

    ![配置 Akamai](./media/header-akamai-tutorial/configure12.png)

1. 单击“保存并转到‘部署’”按钮。

    ![配置 Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>部署应用程序

1. 单击“部署应用程序”按钮。

    ![配置 Akamai](./media/header-akamai-tutorial/configure14.png)

1. 验证是否已成功部署应用程序。

    ![配置 Akamai](./media/header-akamai-tutorial/configure15.png)

1. 最终用户体验。

    ![配置 Akamai](./media/header-akamai-tutorial/enduser01.png)

    ![配置 Akamai](./media/header-akamai-tutorial/enduser02.png)

1. 条件访问。

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>远程桌面

1. 从“添加应用程序向导”中选择“RDP”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure16.png)

1. 输入应用程序名称和描述 。

    ![配置 Akamai](./media/header-akamai-tutorial/configure17.png)

    ![配置 Akamai](./media/header-akamai-tutorial/configure18.png)

1. 指定提供服务的连接器。

    ![配置 Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>身份验证

单击“保存并转到‘服务’”。

![配置 Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>服务

单击“保存并转到‘高级设置’”。

![配置 Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>高级设置

1. 单击“保存并转到‘部署’”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure22.png)

    ![配置 Akamai](./media/header-akamai-tutorial/configure23.png)

    ![配置 Akamai](./media/header-akamai-tutorial/configure24.png)

1. 最终用户体验

    ![配置 Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![配置 Akamai](./media/header-akamai-tutorial/enduser02.png)

1. 条件性访问

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess06.png)

1. 此外，也可直接键入 RDP 应用程序 URL。

#### <a name="ssh"></a>SSH

1. 转到“添加应用程序”，然后选择“SSH”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure25.png)

1. 输入应用程序名称和描述 。

    ![配置 Akamai](./media/header-akamai-tutorial/configure26.png)

1. 配置应用程序标识

    ![配置 Akamai](./media/header-akamai-tutorial/configure27.png)

    a. 指定“名称”/“说明”。

    b. 指定用于 SSH 连接的应用程序服务器 IP/FQDN 和端口。

    c. 指定“SSH 用户名”/“通行短语”*请检查 Akamai EAA。

    d. 指定“外部主机名”。

    e. 指定“连接器位置”，并选择连接器。

#### <a name="authentication"></a>身份验证

单击“保存并转到‘服务’”。

![配置 Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>服务

单击“保存并转到‘高级设置’”。

![配置 Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>高级设置

单击“保存并转到‘部署’”

![配置 Akamai](./media/header-akamai-tutorial/configure30.png)

![配置 Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>部署

1. 单击“部署应用程序”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure32.png)

1. 最终用户体验

    ![配置 Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![配置 Akamai](./media/header-akamai-tutorial/enduser04.png)

1. 条件性访问

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Kerberos 身份验证

在下面的示例中，我们将使用 KCD 发布内部 Web 服务器 <code>http://frp-app1.superdemo.live</code> 并启用 SSO

#### <a name="general-tab"></a>“常规”选项卡

![配置 Akamai](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>“身份验证”标签页

分配标识提供者

![配置 Akamai](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>“服务”选项卡

![配置 Akamai](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>高级设置

![配置 Akamai](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> Web 服务器的 SPN 的格式为 SPN@Domain，例如本演示中的 `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`。 将其余设置保留为默认值。

#### <a name="deployment-tab"></a>“部署”选项卡

![配置 Akamai](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>添加目录

1. 从下拉列表中选择“AD”。

    ![配置 Akamai](./media/header-akamai-tutorial/configure33.png)

1. 提供必要的数据。

    ![配置 Akamai](./media/header-akamai-tutorial/configure34.png)

1. 验证目录创建情况。

    ![配置 Akamai](./media/header-akamai-tutorial/directorydomain.png)

1. 添加需要访问的组/OU。

    ![配置 Akamai](./media/header-akamai-tutorial/addgroup.png)

1. 下面的组名为 EAAGroup，有 1 个成员。

    ![配置 Akamai](./media/header-akamai-tutorial/eaagroup.png)

1. 通过单击“标识” > “标识提供者”将目录添加到标识提供者，然后单击“目录”选项卡并单击“分配目录”   。

    ![配置 Akamai](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>为 EAA 演练配置 KCD 委派

#### <a name="step-1-create-an-account"></a>步骤 1：创建帐户 

1. 在本示例中，我们将使用名为 EAADelegation 的帐户。 可使用 Active Directory 用户和计算机管理单元执行此操作。

    ![配置 Akamai](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > 用户名必须采用特定格式，根据标识截获名称而定。 从图 1 中可看出它是 corpapps.login.go.akamai-access.com

1. 用户登录名将为：`HTTP/corpapps.login.go.akamai-access.com`

    ![配置 Akamai](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>步骤 2：为此帐户配置 SPN

1. 根据此示例，SPN 将如下所示。

2. setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation

    ![配置 Akamai](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>步骤 3：配置委派

1. 对于 EAADelegation 帐户，请单击“委派”选项卡。

    ![配置 Akamai](./media/header-akamai-tutorial/spn.png)

    * 指定使用任意身份验证协议
    * 单击“添加”，然后添加 Kerberos 网站的应用池帐户。 如果配置正确，应自动解析为正确的 SPN。

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>步骤 4：为 AKAMAI EAA 创建 Keytab 文件

1. 下面是通用语法。

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. 示例说明

    | 片段 | 说明 |
    | - | - |
    | Ktpass /out EAADemo.keytab | // 输出 Keytab 文件的名称 |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA 委派帐户 |
    | /pass RANDOMPASS | // EAA 委派帐户密码 |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // 查阅 Akamai EAA 文档 |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![配置 Akamai](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>步骤 5：在 AKAMAI EAA 控制台中导入 Keytab

1. 单击“系统” > “Keytab” 。

    ![配置 Akamai](./media/header-akamai-tutorial/keytabs.png)

1. 在 Keytab 类型中，选择“Kerberos 委派”。

    ![配置 Akamai](./media/header-akamai-tutorial/keytabdelegation.png)

1. 确保 Keytab 显示为“已部署且已验证”。

    ![配置 Akamai](./media/header-akamai-tutorial/keytabs02.png)

1. 用户体验

    ![配置 Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![配置 Akamai](./media/header-akamai-tutorial/enduser04.png)

1. 条件性访问

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![配置 Akamai](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>创建 Akamai 测试用户

在本部分，你将在 Akamai 中创建名为 B.Simon 的用户。 请与 [Akamai 客户端支持团队](https://www.akamai.com/us/en/contact-us/)协作来在 Akamai 平台中添加用户。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Akamai”磁贴时，应会自动登录到设置了 SSO 的 Akamai。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Akamai](https://aad.portal.azure.com/)
