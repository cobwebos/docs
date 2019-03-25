---
title: 教程：Azure Active Directory 与 Brightidea 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Brightidea 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc45c63d3168a15b4701dcaff7bb531f759d47ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888700"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>教程：Azure Active Directory 与 Brightidea 的集成

本教程介绍如何将 Brightidea 与 Azure Active Directory (Azure AD) 集成。
将 Brightidea 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Brightidea。
* 可让用户使用其 Azure AD 帐户自动登录到 Brightidea（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Brightidea 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Brightidea 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。


* Brightidea 支持 **SP 和 IDP** 发起的 SSO
* Brightidea 支持**实时**用户预配


## <a name="adding-brightidea-from-the-gallery"></a>从库中添加 Brightidea

若要配置 Brightidea 与 Azure AD 的集成，需要从库中将 Brightidea 添加到托管 SaaS 应用列表。

**若要从库中添加 Brightidea，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Brightidea**，在结果面板中选择“Brightidea”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的“Brightidea”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Brightidea 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Brightidea 相关用户之间建立链接关系。

若要配置并测试 Brightidea 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Brightidea 单一登录](#configure-brightidea-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Brightidea 测试用户](#create-brightidea-test-user)** - 在 Brightidea 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Brightidea 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Brightidea”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果已获取“服务提供商元数据文件”并想要在 **IDP** 发起的模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤：

    a. 单击“上传元数据文件”。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“Brightidea”部分的文本框中：

    ![图像](common/idp-intiated.png)

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据要求手动填充这些值。

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![图像](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.brightidea.com`

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Brightidea”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-brightidea-single-sign-on"></a>配置 Brightidea 单一登录

1. 在另一个 Web 浏览器窗口中，使用管理员凭据登录到 Brightidea。

2. 若要在 Brightidea 系统中使用 SSO 功能，请导航到“企业设置” -> “身份验证”选项卡。此时会看到两个子选项卡：“身份验证选择”和“SAML 配置文件”。

    ![Brightidea 配置](./media/brightidea-tutorial/configure1.png)

3. 选择“身份验证选择”。 默认只会显示两种标准方法：Brightidea 登录和注册。 添加 SSO 方法后，它会显示在列表中。

    ![Brightidea 配置](./media/brightidea-tutorial/configure2.png)

4. 选择“SAML 配置文件”并执行以下步骤：

    ![Brightidea 配置](./media/brightidea-tutorial/configure3.png)

    a. 单击“下载元数据”，然后在 Azure 门户中的“基本 SAML 配置”部分上传。

    b. 单击“标识提供者设置”下的“新增”按钮并执行以下步骤：
    
    ![Brightidea 配置](./media/brightidea-tutorial/configure4.png)
    
   * 输入“SAML 配置文件名称”，例如 `Azure Ad SSO`
    
   * 对于“上传元数据”，请单击“选择文件”，然后上传从 Azure 门户下载的元数据文件。

     > [!NOTE]
     > 上传元数据文件后，剩余的字段“单一登录服务”、“标识提供者颁发者”和“上传公钥”将自动填充。

   * 在“电子邮件”文本框中，输入 `mail` 作为值。
     
   * 在“屏幕名称”文本框中，输入 `givenName` 作为值。
     
   * 单击“保存更改”。  

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Brightidea 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Brightidea”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Brightidea”。

    ![“应用程序”列表中的“Brightidea”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-brightidea-test-user"></a>创建 Brightidea 测试用户

在本部分，我们将在 Brightidea 中创建名为 Britta Simon 的用户。 Brightidea 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Brightidea 中尚不存在用户，身份验证后会创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Brightidea”磁贴时，应会自动登录到设置了 SSO 的 Brightidea。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

