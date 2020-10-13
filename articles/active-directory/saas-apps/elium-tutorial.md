---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Elium 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Elium 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: c4ac9e182a7504ea96f73f2ab95d50fb9d859e47
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665219"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Elium 的集成

本教程介绍如何将 Elium 与 Azure Active Directory (Azure AD) 集成。 将 Elium 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Elium。
* 让用户使用其 Azure AD 帐户自动登录到 Elium。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Elium 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Elium 支持 **SP 和 IDP** 发起的 SSO
* Elium 支持**实时**用户预配

## <a name="adding-elium-from-the-gallery"></a>从库中添加 Elium

若要配置 Elium 与 Azure AD 的集成，需要从库中将 Elium 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Elium”********。
1. 在结果面板中选择“Elium”，然后添加该应用****。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>配置并测试 Elium 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Elium 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Elium 相关用户之间建立链接关系。

若要配置并测试 Elium 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Elium SSO](#configure-elium-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Elium 测试用户](#create-elium-test-user)** - 在 Elium 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Elium”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/acs`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > 这些不是实际值。 从可以在 `https://<platform-domain>.elium.com/login/saml2/metadata` 中下载的 **SP 元数据文件**获取这些值。本教程稍后将对此做出说明。

1. Elium 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性，Elium 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| ----------------|
    | 电子邮件   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > 这是默认声明。 **只需电子邮件声明**。 对于 JIT 预配，也只需电子邮件声明。 其他自定义声明根据客户平台的不同而异。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Elium”部分中，根据要求复制相应的 URL****。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Elium 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Elium”****。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-elium-sso"></a>配置 Elium SSO

1. 若要在“Elium”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Elium”，此时会将你定向到 Elium 应用程序  。 在此处提供管理员凭据以登录到 Elium。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Elium，请打开新的 Web 浏览器窗口，以管理员身份登录到 Elium 公司站点，并执行以下步骤：

1. 在右上角单击“用户配置文件”，然后选择“设置”。 

    ![配置单一登录 Elium 01](./media/elium-tutorial/elium-01.png)

1. 在“高级”下选择“安全性” 。

    ![配置单一登录 Elium 02](./media/elium-tutorial/elium-02.png)

1. 向下滚动到“单一登录(SSO)”部分中，并执行以下步骤： 

    ![配置单一登录 Elium 03](./media/elium-tutorial/elium-03.png)

    a. 复制“验证 SAML2 身份验证是否适用于你的帐户”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   

    > [!NOTE]
    > 配置 SSO 后，始终可以通过以下 URL 访问默认的远程登录页：`https://<platform_domain>/login/regular/login` 

    b. 选中“启用 SAML2 联合身份验证”复选框。 

    c. 选中“JIT 预配”复选框。 

    d. 单击“下载”按钮打开“SP 元数据”。  

    e. 在“SP 元数据”文件中搜索 **entityID**，复制“entityID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中。     

    ![配置单一登录 Elium 04](./media/elium-tutorial/elium-04.png)

    f. 在“SP 元数据”文件中搜索 **AssertionConsumerService**，复制“Location”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。    

    ![配置单一登录 Elium 05](./media/elium-tutorial/elium-05.png)

    g. 在写字板中打开从 Azure 门户下载的元数据文件内容，复制内容并将其粘贴到“IdP 元数据”文本框中  。

    h. 单击“ **保存**”。

### <a name="create-elium-test-user"></a>创建 Elium 测试用户

本部分将在 Elium 中创建一个名为 B.Simon 的用户。 Elium 支持默认已启用的**实时预配**。 此部分不存在任何操作项。 尝试访问 Elium 时，如果 Elium 中尚不存在用户，则系统会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [Elium 支持团队](mailto:support@elium.com)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 
 
#### <a name="sp-initiated"></a>SP 启动的：
 
* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Elium 登录 URL，可以在其中启动登录流。  
 
* 直接转到 Elium 登录 URL，并在其中启动登录流。
 
#### <a name="idp-initiated"></a>IDP 启动的：
 
* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Elium 
 
还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 在点击访问面板中的 Elium 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Elium。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Elium 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
