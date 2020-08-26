---
title: 教程：Azure Active Directory 与 Cloud Academy - SSO 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Cloud Academy - SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 88e626f9b3069b3b43d525914c017caf763a9047
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Cloud Academy - SSO 的集成

本教程介绍了如何将 Cloud Academy - SSO 与 Azure Active Directory (Azure AD) 集成。 将 Cloud Academy - SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cloud Academy - SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Cloud Academy - SSO。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Cloud Academy - SSO 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Cloud Academy - SSO 支持 SP 发起的 SSO

* 配置 Cloud Academy - SSO 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>从库中添加 Cloud Academy - SSO

若要配置 Cloud Academy - SSO 与 Azure AD 的集成，需要从库中将 Cloud Academy - SSO 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分中，在搜索框中键入“Cloud Academy - SSO”。
1. 从结果面板中选择“Cloud Academy - SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>配置并测试 Cloud Academy - SSO 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Cloud Academy - SSO 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Cloud Academy - SSO 中的相关用户之间建立链接关系。

若要配置并测试 Cloud Academy - SSO 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Cloud Academy-SSO SSO](#configure-cloud-academy-sso-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Cloud Academy-SSO 测试用户](#create-cloud-academy-sso-test-user) - 在 Cloud Academy - SSO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 Cloud Academy - SSO 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    在“登录 URL”文本框中，键入 URL：`https://cloudacademy.com/login/enterprise/`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分，你将通过授予 B.Simon 访问 Cloud Academy - SSO 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中选择“Cloud Academy - SSO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cloud-academy-sso-sso"></a>配置 Cloud Academy-SSO SSO

1. 在其他浏览器窗口中，以管理员身份登录 Cloud Academy - SSO 公司站点。

1. 单击公司的名称，并从菜单中选择“设置和集成”。

    ![配置 ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. 在“设置和集成”页上，单击“集成”选项卡，然后单击“SSO”卡。

    ![配置 ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. 在下面的页中执行以下步骤：

    ![配置 ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. 在“实体 ID URL”文本框中，粘贴从 Azure 门户复制的“实体 ID”值。

    b. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    c. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“证书”文本框中 。

    d. 在“名称 ID 格式”文本框中，默认值为 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. 单击“保存”按钮。

    > [!NOTE]
    > 有关如何配置 Cloud Academy - SSO 的详细信息，请参阅[支持文章](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)。

### <a name="create-cloud-academy-sso-test-user"></a>创建 Cloud Academy-SSO 测试用户

1. 登录到“Cloud Academy - SSO”。

1. 单击公司名称，并从菜单中选择“成员”。

    ![ 创建测试用户 ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. 单击“邀请成员”并选择“邀请单个成员”。

    ![ 创建测试用户 ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. 输入必填字段，然后单击“邀请”。

    ![ 创建测试用户 ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Cloud Academy - SSO”磁贴时，应会自动登录到设置了 SSO 的 Cloud Academy - SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Cloud Academy - SSO](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Cloud Academy - SSO](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)