---
title: 教程：Azure Active Directory 与 Salesforce 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Salesforce 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27a61205426cbf43fd3b3b549909ffa13ff07dc7
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500356"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>教程：Azure Active Directory 与 Salesforce 集成

在本教程中，了解如何将 Salesforce 与 Azure Active Directory (Azure AD) 集成。
将 Salesforce 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Salesforce。
* 可让用户使用其 Azure AD 帐户自动登录到 Salesforce（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Salesforce 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Salesforce 支持 SP 发起的 SSO

* Salesforce 支持实时用户预配

* Salesforce 支持[自动用户预配](salesforce-provisioning-tutorial.md)

## <a name="adding-salesforce-from-the-gallery"></a>从库中添加 Salesforce

若要配置 Salesforce 与 Azure AD 的集成，需要从库中将 Salesforce 添加到托管的 SaaS 应用列表。

**若要从库中添加 Salesforce，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 Salesforce，在结果面板中选择“Salesforce”，并单击“添加”按钮来添加该应用程序。

    ![结果列表中的 Salesforce](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Salesforce 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Salesforce 相关用户之间建立链接关系。

若要配置和测试 Salesforce 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Salesforce 单一登录](#configure-salesforce-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Salesforce 测试用户](#create-salesforce-test-user) - 在 Salesforce 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Salesforce 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Salesforce”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Salesforce 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入值：

    企业帐户： `https://<subdomain>.my.salesforce.com`

    开发人员帐户： `https://<subdomain>-dev-ed.my.salesforce.com`

    b. 在“标识符”文本框中，使用以下模式键入值：

    企业帐户： `https://<subdomain>.my.salesforce.com`

    开发人员帐户： `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)获取这些值。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Salesforce”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-salesforce-single-sign-on"></a>配置 Salesforce 单一登录

1. 在浏览器中打开新选项卡并登录到 Salesforce 管理员帐户。

2. 单击页面右上角设置图标下的“安装”。

    ![配置单一登录](./media/salesforce-tutorial/configure1.png)

3. 向下滚动到导航窗格中的“设置”，单击“标识”，以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso.png)

4. 在“单一登录设置”页上，单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > 如果无法为 Salesforce 帐户启用单一登录设置，则需要联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)。

5. 选择“已启用 SAML”，并单击“保存”。

      ![配置单一登录](./media/salesforce-tutorial/sf-enable-saml.png)

6. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. 单击“选择文件”以上传从 Azure 门户下载的元数据 XML 文件，然后单击“创建”。

    ![配置单一登录](./media/salesforce-tutorial/xmlchoose.png)

8. 在“SAML 单一登录设置”页上，字段将自动填充，请单击“保存”。

    ![配置单一登录](./media/salesforce-tutorial/salesforcexml.png)

9. 在 Salesforce 的左侧导航窗格中，单击“公司设置”展开相关部分，然后单击“我的域”。

    ![配置单一登录](./media/salesforce-tutorial/sf-my-domain.png)

10. 向下滚动到“身份验证配置”部分，并单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. 在“身份验证配置”部分中，选中“AzureSSO”作为 SAML SSO 配置的“身份验证服务”，然后单击“保存”。

    ![配置单一登录](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 如果选择了多个身份验证服务，则当用户向 Salesforce 环境发起单一登录时，系统将提示他们选择登录时要使用的身份验证服务。 如果不希望发生这种情况，应将其他所有身份验证服务保持未选中状态。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 `brittasimon\@yourcompanydomain.extension`。 例如，BrittaSimon@contoso.com。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Salesforce”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Salesforce”。

    ![应用程序列表中的 Salesforce 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-salesforce-test-user"></a>创建 Salesforce 测试用户

在本部分中，将在 Salesforce 中创建名为 Britta Simon 的用户。 Salesforce 支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 时，如果 Salesforce 中没有用户，系统会创建一个新用户。 Salesforce 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-provisioning-tutorial.md)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Salesforce 磁贴时，应当会自动登录到为其设置了 SSO 的 Salesforce。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](salesforce-provisioning-tutorial.md)
