---
title: 教程：Azure Active Directory 与 Coralogix 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Coralogix 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678286"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>教程：Azure Active Directory 与 Coralogix 集成

本教程介绍如何将 Coralogix 与 Azure Active Directory (Azure AD) 集成。
将 Coralogix 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Coralogix。
* 可让用户使用其 Azure AD 帐户自动登录到 Coralogix（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Coralogix 的集成，需要以下项：

- 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
- 已启用 Coralogix 单一登录的订阅。 

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Coralogix 支持 SP 发起的 SSO。

## <a name="add-coralogix-from-the-gallery"></a>从库中添加 Coralogix

若要配置 Coralogix 与 Azure AD 的集成，请先从库中将 Coralogix 添加到托管 SaaS 应用列表。

若要从库中添加 Coralogix，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 **Coralogix**。 在结果窗格中选择“Coralogix”，然后选择“添加”按钮添加该应用程序。

     ![结果列表中的 Coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 Britta Simon 的测试用户来配置并测试 Coralogix 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Coralogix 相关用户之间建立链接关系。

若要配置并测试 Coralogix 的 Azure AD 单一登录，请先完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [配置 Coralogix 单一登录](#configure-coralogix-single-sign-on) - 在应用程序端配置单一登录设置。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Coralogix 测试用户](#create-a-coralogix-test-user) - 在 Coralogix 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Coralogix 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Coralogix”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML”启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”对话框中执行以下步骤：

    ![Coralogix 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”框中，使用以下模式输入 URL：`https://<SUBDOMAIN>.coralogix.com`。

    b. 在“标识符(实体 ID)”文本框中输入 URL，例如：
    
    `https://api.coralogix.com/saml/metadata.xml`

    或

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新该值。 请联系 [Coralogix 客户端支持团队](mailto:info@coralogix.com)获取该值。 也可以参考 Azure 门户中的“基本 SAML 配置”部分显示的模式。

5. Coralogix 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，选择“编辑”按钮打开“用户属性”对话框。

    ![image](common/edit-attribute.png)

6. 在“用户声明”部分的“用户属性”对话框中，使用“编辑”图标编辑声明。 还可以使用“添加新声明”来添加声明，以配置 SAML 令牌属性，如上图所示。 然后执行以下步骤：
    
    a. 选择“编辑”图标打开“管理用户声明”对话框。

    ![插图](./media/coralogix-tutorial/tutorial_usermail.png) ![插图](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. 从“选择名称标识符格式”列表中，选择“电子邮件地址”。

    c. 在“源属性”列表中，选择 **user.mail**。

    d. 选择“保存”。

7. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据要求从给定的选项下载“联合元数据 XML”。 然后将证书保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

8. 在“设置 Coralogix”部分，复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-coralogix-single-sign-on"></a>配置 Coralogix 单一登录

若要在 Coralogix 端配置单一登录，请将下载的“联合元数据 XML”以及从 Azure 门户复制的 URL 发送给 [Coralogix 支持团队](mailto:info@coralogix.com)。 他们将确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 在屏幕顶部选择“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户”对话框中执行以下步骤：

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中输入“brittasimon@yourcompanydomain.extension”。 例如，在本例中，可以输入“brittasimon@contoso.com”。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Coralogix 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Coralogix”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Coralogix”。

    ![应用程序列表中的 Coralogix 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”按钮。 然后，在“添加分配”对话框中，选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表内的“Britta Simon”。 然后单击屏幕底部的“选择”按钮。

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="create-a-coralogix-test-user"></a>创建 Coralogix 测试用户

在本部分，将在 Coralogix 中创建一个名为“Britta Simon”的用户。 在  [Coralogix 支持团队](mailto:info@coralogix.com)的配合下，将用户添加到 Coralogix 平台。 必须先创建并激活用户，然后才能使用单一登录。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分，我们将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Coralogix”磁贴时，应会自动登录到 Coralogix。 有关“我的应用”门户的详细信息，请参阅[什么是“我的应用”门户？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

