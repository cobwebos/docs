---
title: 教程：教程：Azure Active Directory 与 Cisco Webex 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Cisco Webex 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: e718d37bc1d34bccf1ea8d0ce2607ff83fc355d1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824400"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教程：Azure Active Directory 与 Cisco Webex 的集成

本教程介绍如何将 Cisco Webex 与 Azure Active Directory (Azure AD) 集成。
将 Cisco Webex 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Cisco Webex。
* 可以让用户使用其 Azure AD 帐户自动登录到 Cisco Webex（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cisco Webex 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Cisco Webex 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Cisco Webex 支持 SP 发起的 SSO

* Cisco Webex 支持“恰时”用户预配

* Cisco Webex 支持**自动用户预配**

## <a name="adding-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex

若要配置 Cisco Webex 与 Azure AD 的集成，需要从库中将 Cisco Webex 添加到托管 SaaS 应用的列表。

**若要从库中添加 Cisco Webex，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Cisco Webex”，在结果面板中选择“Cisco Webex”，然后单击“添加”按钮以添加该应用程序。

     ![结果列表中的 Cisco Webex](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Cisco Webex 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Cisco Webex 中相关用户之间建立链接关系。

若要配置和测试 Cisco Webex 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Cisco Webex 单一登录](#configure-cisco-webex-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Cisco Webex 测试用户](#create-cisco-webex-test-user)** - 在 Cisco Webex 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Cisco Webex 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Cisco Webex”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cisco Webex 公司网站。

4. 从菜单的左侧中单击“设置”。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. 在“设置”页面上，在“身份验证”部分下向下滚动，单击“修改”。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. 选择“集成第三方标识提供者。(高级)”，然后转到下一个屏幕。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. 在“导出目录元数据”页面上，单击“下载元数据文件”以下载元数据文件。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. 在 Azure 门户中的“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

9. 在“基本 SAML 配置”部分中，通过执行以下步骤上传已下载的**服务提供程序元数据**文件并配置应用程序：

    a. 单击“上传元数据文件”。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功完成服务提供程序元数据文件的上传后，**标识符**和**回复 URL** 值将自动填充在“基本 SAML 配置”部分：

    ![Cisco Webex 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.webex.com/`

    > [!Note]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Cisco Webex 客户端支持团队](https://www.webex.co.in/support/support-overview.html)获取此值。

10. Cisco Webex 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

11. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 名称 | 源属性|
    | ---------------| --------------- | --------- |
    |   名    | user.givenname |
    |   姓    | user.surname |
    |   uid    | user.mail |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

12. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

13. 在“设置 Cisco Webex”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-cisco-webex-single-sign-on"></a>配置 Cisco Webex 单一登录

1. 在 Cisco Webex 公司站点管理员页面上，使用文件浏览器选项找到并上传 Azure AD 元数据文件。 然后，选择“元数据中需要由证书颁发机构签名的证书(更安全)”并转到下一个屏幕。 

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. 选择“测试 SSO 连接”，当新的浏览器标签页打开时，通过登录使用 Azure AD 进行身份验证。

3. 返回到 **Cisco Cloud Collaboration Management** 浏览器标签页。如果测试成功，则选择“此测试成功。启用单一登录”，并单击“保存”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 Cisco Webex 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Cisco Webex”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cisco Webex”。

    ![应用程序列表中的 Cisco Webex 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-cisco-webex-test-user"></a>创建 Cisco Webex 测试用户

本部分的目的是在 Cisco Webex 中创建名为 Britta Simon 的用户。 Cisco Webex 支持实时预配和自动用户预配，这是默认启用的。 此部分不存在任何操作项。 尝试访问 Cisco Webex 时，如果 Cisco Webex 中尚不存在用户，则系统会创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Cisco Webex 磁贴时，应当会自动登录到你为其设置了 SSO 的 Cisco Webex。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

