---
title: 教程：Azure Active Directory 与 Palo Alto Networks - Aperture 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks - Aperture 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: d7f0ca619c990d2a42c31df82ee9f90bd7ea230b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801811"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>教程：Azure Active Directory 与 Palo Alto Networks - Aperture 集成

在本教程中，了解如何将 Palo Alto Networks - Aperture 与 Azure Active Directory (Azure AD) 进行集成。
将 Palo Alto Networks - Aperture 与 Azure AD 集成可提供以下好处：

* 可在 Azure AD 中控制谁有权访问 Palo Alto Networks - Aperture。
* 可以让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks - Aperture（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks - Aperture 的集成，需具有以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Palo Alto Networks - Aperture 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Palo Alto Networks - Aperture 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>从库添加 Palo Alto Networks - Aperture

若要配置 Palo Alto Networks - Aperture 与 Azure AD 的集成，需要从库中将 Palo Alto Networks - Aperture 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Palo Alto Networks - Aperture”。 
1. 在结果面板中选择“Palo Alto Networks - Aperture”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

在本部分中，我们将基于名为“B.Simon”的测试用户为 Palo Alto Networks - Aperture 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Palo Alto Networks - Aperture 中相关用户之间建立链接关系。

若要为 Palo Alto Networks - Aperture 配置和测试 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 Palo Alto Networks - Aperture SSO](#configure-palo-alto-networks---aperture-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Palo Alto Networks - Aperture 测试用户](#create-palo-alto-networks---aperture-test-user)** - 在 Palo Alto Networks - Aperture 中创建 Britta Simon 的对应用户，以链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Palo Alto Networks - Aperture”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Palo Alto Networks - Aperture 域和 URL 单一登录信息 IDP](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Palo Alto Networks - Aperture 域和 URL 单一登录信息 SP](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [Palo Alto Networks - Aperture 客户端支持团队](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Palo Alto Networks - Aperture”**** 部分中，根据要求复制相应的 URL。

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

在本部分，我们将通过授予 B.Simon 访问 Palo Alto Networks - Aperture 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Palo Alto Networks - Aperture”****。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-palo-alto-networks---aperture-sso"></a>配置 Palo Alto Networks - Aperture SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Palo Alto Networks - Aperture。

2. 单击顶部菜单中的“设置”****。

    ![“设置”选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. 导航到“应用程序”部分，单击菜单左侧的“身份验证”窗体。********

    ![“身份验证”选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. 在“身份验证”页上，执行以下步骤：
    
    ![身份验证选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. 在“单一登录”字段中选中“启用单一登录(支持的 SSP 提供者为 Okta, One login)”。********

    b. 在“标识提供者 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值********。

    c. 在“标识提供者证书”字段中单击“选择文件”****，上传从 Azure AD 下载的证书。****

    d. 在“标识提供者 SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 查看“Aperture 信息”部分中的 IdP 信息，并从“Aperture 密钥”字段下载证书。********

    f. 单击“保存”  。


### <a name="create-palo-alto-networks---aperture-test-user"></a>创建 Palo Alto Networks - Aperture 测试用户

在本部分中，在 Palo Alto Networks - Aperture 中创建名为 Britta Simon 的用户。 与 [Palo Alto Networks - Aperture 客户支持团队](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)协作，在 Palo Alto Networks - Aperture 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Palo Alto Networks - Aperture 登录 URL，你可以在其中启动登录流。  

* 直接转到 Palo Alto Networks - Aperture 登录 URL，在其中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Palo Alto Networks - Aperture 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 单击访问面板中的“Palo Alto Networks - Aperture”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，你会自动登录到为其设置了 SSO 的 Palo Alto Networks - Aperture。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 Palo Alto Networks - Aperture 后，就可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
