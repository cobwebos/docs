---
title: 教程：Azure Active Directory 与 Saba TalentSpace 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Saba TalentSpace 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Saba TalentSpace 的集成

本教程介绍如何将 Saba TalentSpace 与 Azure Active Directory (Azure AD) 集成。 将 Saba TalentSpace 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Saba TalentSpace。
* 让用户使用其 Azure AD 帐户自动登录到 Saba TalentSpace。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Saba TalentSpace 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Saba TalentSpace 支持 **SP** 发起的 SSO
* 配置 Saba TalentSpace 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-saba-talentspace-from-the-gallery"></a>从库中添加 Saba TalentSpace

若要配置 Saba TalentSpace 与 Azure AD 的集成，需要从库中将 Saba TalentSpace 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Saba TalentSpace**。 
1. 在结果面板中选择“Saba TalentSpace”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>配置并测试 Saba TalentSpace 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Saba TalentSpace 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Saba TalentSpace 中的相关用户之间建立链接关系。

若要配置并测试 Saba TalentSpace 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Saba TalentSpace 测试用户](#create-saba-talentspace-test-user)** - 在 Saba TalentSpace 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Saba TalentSpace”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://global.hgncloud.com/[companyname]/saml/login` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://global.hgncloud.com/[companyname]/saml/metadata` 

    c. 在“回复 URL (断言使用者服务 URL)”  文本框中，使用以下模式键入 URL：`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Saba TalentSpace 客户端支持团队](https://support.saba.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Saba TalentSpace”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Saba TalentSpace 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Saba TalentSpace”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-saba-talentspace-sso"></a>配置 Saba TalentSpace SSO

1. 在另一个浏览器窗口中，以管理员身份登录到 **Saba TalentSpace** 应用程序。

2. 单击“**选项**”选项卡。
  
    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. 在左侧导航窗格中，单击“SAML 配置”。 
  
    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. 在“SAML 配置”  页上，执行以下步骤：

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. 选择 **NameID** 作为**唯一标识符**。

    b. 对于“唯一标识符映射到”，请选择“用户名”  。 
  
    c. 要上载已下载的元数据文件，请单击“浏览”选择该文件，并单击“上载文件”。  

    d. 若要测试配置，请单击“运行测试”。 

    > [!NOTE]
    > 需要等待消息“*SAML 测试已完成。请关闭此窗口*”。 然后，关闭打开的浏览器窗口。 仅在测试完成的情况下，才会启用“启用 SAML”复选框。 

    e. 选择“启用 SAML”  。

    f. 单击 **“保存更改”** 。

### <a name="create-saba-talentspace-test-user"></a>创建 Saba TalentSpace 测试用户

本部分的目的是在 Saba TalentSpace 中创建名为 Britta Simon 的用户。

**若要在 Saba TalentSpace 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 **Saba TalentSpace** 应用程序。

2. 单击“用户中心”选项卡，并单击“创建用户”。  

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. 在“新建用户”  对话框页上，执行以下步骤：

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. 在“名字”文本框中键入用户的名字，例如 **B**。 

    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）   。

    c. 在“用户名”文本框中键入 **B.Simon**（与 Azure 门户中相同的用户名）。 

    d. 在“密码”文本框中，键入 B.Simon 的密码。 

    e. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Saba TalentSpace”磁贴时，应会自动登录到设置了 SSO 的 Saba TalentSpace。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Saba TalentSpace](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)