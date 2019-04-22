---
title: 教程：Azure Active Directory 与 Tableau Online 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Tableau Online 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 563668780c42ca23a1904757f886581f3ad0c5e8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617297"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>教程：Azure Active Directory 与 Tableau Online 集成

在本教程中，了解如何将 Tableau Online 与 Azure Active Directory (Azure AD) 集成。
将 Tableau Online 与 Azure AD 集成提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Tableau Online。
* 可让用户使用其 Azure AD 帐户自动登录到 Tableau Online（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Tableau Online 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Tableau Online 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Tableau Online 支持 **SP** 发起的 SSO

## <a name="adding-tableau-online-from-the-gallery"></a>从库中添加 Tableau Online

要配置 Tableau Online 与 Azure AD 的集成，需要从库中将 Tableau Online 添加到托管 SaaS 应用列表。

**若要从库中添加 Tableau Online，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Tableau Online**，在结果面板中选择“Tableau Online”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的“Tableau Online”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Tableau Online 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Tableau Online 相关用户之间建立链接关系。

若要配置和测试 Tableau Online 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Tableau Online 单一登录](#configure-tableau-online-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Tableau Online 测试用户](#create-tableau-online-test-user)** - 在 Tableau Online 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Tableau Online 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Tableau Online”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Tableau Online 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，键入 URL：`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > 本教程的“设置 Tableau Online”部分会提供 `<entityid>` 值。 实体 ID 值是“设置 Tableau Online”部分提供的“Azure AD 标识符”值。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Tableau Online”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-tableau-online-single-sign-on"></a>配置 Tableau Online 单一登录

1. 在另一个浏览器窗口中，登录 Tableau Online 应用程序。 转到“设置”，然后到“身份验证”。

    ![配置单一登录](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. 若要启用 SAML，请在“身份验证类型”部分下， 依次选中“启用附加的身份验证方法”、“SAML”复选框。

    ![配置单一登录](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 向下滚动到“将元数据文件导入 Tableau Online”部分。  单击“浏览”并导入已从 Azure AD 下载的元数据文件。 然后，单击“应用”。

   ![配置单一登录](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. 在“匹配断言”部分中，为**电子邮件地址**、**名字**和**姓氏**插入相应的标识提供者断言名称。 若要从 Azure AD 中获取此信息，请执行以下操作： 
  
    a. 在 Azure 门户中，转到“Tableau Online”应用程序集成页。

    b. 在“用户属性和声明”部分，单击编辑图标。

   ![配置单一登录](./media/tableauonline-tutorial/attributesection.png)

    c. 通过执行以下步骤复制以下属性的命名空间值：givenname、email 和 surname：

   ![Azure AD 单一登录](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. 单击“user.givenname”值

    e. 复制“命名空间”文本框中的值。

    ![配置单一登录](./media/tableauonline-tutorial/attributesection2.png)

    f. 若要复制 email 和 surname 的命名空间值，请重复上述步骤。

    g. 切换到 Tableau Online 应用程序，然后按如下所示设置“用户属性和声明”部分：

    * 电子邮件：**mail** 或 **userprincipalname**

    * 名字：**givenname**

    * 姓氏：**surname**

    ![配置单一登录](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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
    例如，BrittaSimon\@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Tableau Online 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Tableau Online”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Tableau Online”。

    ![“应用程序”列表中的“Tableau Online”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-tableau-online-test-user"></a>创建 Tableau Online 测试用户

在本部分中，会在 Tableau Online 中创建一个名为“Britta Simon”的用户。

1. 在“Tableau Online”上，单击“设置”，然后单击“身份验证”部分。 向下滚动到“管理用户”部分。 依次单击“添加用户”、“输入电子邮件地址”。
  
    ![创建 Azure AD 测试用户](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. 选择“添加用户用于(SAML)身份验证”。 在“输入电子邮件地址”文本框中添加 britta.simon@contoso.com
  
    ![创建 Azure AD 测试用户](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. 单击“添加用户”。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Tableau Online”磁贴时，应会自动登录到设置了 SSO 的 Tableau Online。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
