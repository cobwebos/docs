---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Jamf Pro 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Jamf Pro 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91c8a2fb278515306848f46206db67b7f37ea2ac
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034245"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>教程：Azure Active Directory SSO 与 Jamf Pro 的集成

本教程介绍如何将 Jamf Pro 与 Azure Active Directory (Azure AD) 集成。 将 Jamf Pro 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Jamf Pro。
* 让用户使用其 Azure AD 帐户自动登录到 Jamf Pro。
* 在一个中心位置（Azure 门户）管理帐户。

若要详细了解 SaaS 应用与 Azure AD 的集成，请参阅[使用 Azure Active Directory 进行单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Jamf Pro 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Jamf Pro 支持 **SP** 和 **IdP** 发起的 SSO。

## <a name="add-jamf-pro-from-the-gallery"></a>从库中添加 Jamf Pro

要配置 Jamf Pro 与 Azure AD 的集成，需要从库中将 Jamf Pro 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中，选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，输入 *Jamf Pro*。 
1. 在结果面板中选择“Jamf Pro”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>在 Jamf Pro 的 Azure AD 中配置并测试 SSO

使用名为 B.Simon 的测试用户配置并测试 Jamf Pro 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Jamf Pro 中的相关用户之间建立链接关系。

在本部分，你将配置并测试 Jamf Pro 的 Azure AD SSO。

1. [在 Azure AD 中配置 SSO](#configure-sso-in-azure-ad)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 帐户测试 Azure AD SSO。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够在 Azure AD 中使用 SSO。
1. [在 Jamf Pro 中配置 SSO](#configure-sso-in-jamf-pro)，以在应用程序端配置 SSO 设置。
    1. [创建 Jamf Pro 测试用户](#create-a-jamf-pro-test-user)，在 Jamf Pro 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. [测试 SSO 配置](#test-the-sso-configuration)，验证配置是否正常工作。

## <a name="configure-sso-in-azure-ad"></a>在 Azure AD 中配置 SSO

在本部分，你将在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Jamf Pro”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”。  
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![编辑“基本 SAML 配置”页。](common/edit-urls.png)

1. 若要在“IdP 发起”模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    a. 在“标识符”文本框中，使用以下格式输入 URL：  `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. 在“回复 URL”文本框中，使用以下格式输入 URL：  `https://<subdomain>.jamfcloud.com/saml/SSO`

1. 选择“设置其他 URL”  。 如果要在“SP 发起”模式下配置应用程序，请在“登录 URL”文本框中使用以下格式输入 URL：   `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 你将从 Jamf Pro 门户中的“单一登录”部分获取实际的标识符值（本教程稍后会介绍）。  可以从标识符值提取实际的子域值，并使用该子域信息作为登录 URL 和回复 URL。 还可参考 Azure 门户中的“基本 SAML 配置”部分显示的格式。 

1. 在“设置 SAML 单一登录”页上，转到“SAML 签名证书”部分，选择“复制”按钮以复制“应用联合元数据 URL”，然后将其保存在计算机上。    

    ![SAML 签名证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中，以“[姓名]@[公司域].[扩展]”格式输入用户名。  例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将授予 B.Simon 对 Jamf Pro 的访问权限。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Jamf Pro”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![选择“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”按钮](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择“用户”列表中的“B.Simon”，然后选择屏幕底部的“选择”按钮。   
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，为用户选择相应的角色。  然后选择屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，选择“分配”按钮。  

## <a name="configure-sso-in-jamf-pro"></a>在 Jamf Pro 中配置 SSO 

1. 若要在 Jamf Pro 中自动完成配置，需要选择“安装扩展”来安装“‘我的应用’安全登录浏览器扩展”。  

    ![“我的应用”安全登录浏览器扩展页](common/install-myappssecure-extension.png)

2. 将该扩展添加到浏览器后，选择“设置 Jamf Pro”。  当 Jamf Pro 应用程序打开时，请提供管理员凭据以登录。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-7。

    ![Jamf Pro 中的设置配置页](common/setup-sso.png)

3. 若要手动设置 Jamf Pro，请打开新的 Web 浏览器窗口，并以管理员身份登录到 Jamf Pro 公司站点。 然后执行以下步骤。

4. 选择页面右上角的“设置”图标。 

    ![在 Jamf Pro 中选择“设置”图标](./media/jamfprosamlconnector-tutorial/configure1.png)

5. 选择“单一登录”。 

    ![在 Jamf Pro 中选择“单一登录”](./media/jamfprosamlconnector-tutorial/configure2.png)

6. 在“单一登录”页上执行以下步骤。 

    ![Jamf Pro 中的“单一登录”页](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. 选中“启用单一登录身份验证”复选框。 

    b. 从“标识提供者”下拉菜单中选择选项“其他”。  

    c. 在“其他提供者”字段中输入 **Azure AD**。 

    d. 复制“实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”字段中。   

    > [!NOTE]
    > 使用 `<SUBDOMAIN>` 字段中的值来填写 Azure 门户上“基本 SAML 配置”部分中的登录 URL 和回复 URL。 

    e. 从“标识提供者元数据源”下拉菜单中选择“元数据 URL”。   在显示的字段中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。 

7. 在同一页上，向下滚动到“用户映射”部分。  然后执行以下步骤。   

    ![Jamf Pro 中“单一登录”页的“用户映射”部分。](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. 为“标识提供者用户映射”选择“NameID”选项。   默认情况下，此选项指定为“NameID”，但你可以定义自定义属性。 

    b. 为“JAMF PRO 用户映射”选择“电子邮件”。   Jamf Pro 先按用户，然后按组来映射 IdP 发送的 SAML 属性。 当用户尝试访问 Jamf Pro 时，Jamf Pro 会从标识提供者获取有关该用户的信息，并且其与所有 Jamf Pro 用户帐户相匹配。 如果未找到传入的用户帐户，Jamf Pro 会尝试按组名称进行匹配。

    c. 将值 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 粘贴到“标识提供者组属性名称”字段中。 

    d. 选择“允许用户绕过单一登录身份验证”。  因此，用户将不会重定向到标识提供者登录页进行身份验证，而可以直接登录到 Jamf Pro。 当用户尝试通过标识提供者访问 Jamf Pro 时，会发生 IdP 发起的 SSO 身份验证和授权。

    e. 选择“保存”。 

### <a name="create-a-jamf-pro-test-user"></a>创建一个 Jamf Pro 测试用户

要使 Azure AD 用户能够登录到 Jamf Pro，必须将其预配到 Jamf Pro 中。 Jamf Pro 中的预配是一项手动任务。

若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 Jamf Pro 公司站点。

2. 选择页面右上角的**设置**图标。

    ![Jamf Pro 中的“设置”图标](./media/jamfprosamlconnector-tutorial/configure1.png)

3. 选择“Jamf Pro 用户帐户和组”。 

    ![Jamf Pro 设置中的“Jamf Pro 用户帐户和组”图标](./media/jamfprosamlconnector-tutorial/user1.png)

4. 选择“新建”  。

    ![“Jamf Pro 用户帐户和组”系统设置页](./media/jamfprosamlconnector-tutorial/user2.png)

5. 选择“创建标准帐户”  。

    ![“Jamf Pro 用户帐户和组”页中的“创建标准帐户”选项](./media/jamfprosamlconnector-tutorial/user3.png)

6. 在“新建帐户”对话框中执行以下步骤。 

    ![Jamf Pro 系统设置中的“新建帐户”设置选项](./media/jamfprosamlconnector-tutorial/user4.png)

    a. 在“用户名”字段中，输入测试用户的全名 `Britta Simon`。 

    b. 根据组织的要求选择“访问级别”、“特权集”和“访问状态”的选项。   

    c. 在“全名”字段中输入 `Britta Simon`。 

    d. 在“电子邮件地址”字段中，输入 Britta Simon 帐户的电子邮件地址。 

    e. 在“密码”字段中输入用户的密码。 

    f. 在“验证密码”字段中再次输入用户的密码。 

    g. 选择“保存”。 

## <a name="test-the-sso-configuration"></a>测试 SSO 配置 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Jamf Pro”磁贴时，应会自动登录到配置了 SSO 的 Jamf Pro 帐户。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Jamf Pro](https://aad.portal.azure.com/)

