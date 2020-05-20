---
title: 教程：Azure Active Directory 与 Asana 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Asana 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5531a7c1a95e472239c639e3307623fc4ccedd37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157867"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>教程：Azure Active Directory 与 Asana 集成

在本教程中，了解如何将 Asana 与 Azure Active Directory (Azure AD) 集成。
将 Asana 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Asana。
* 可以让用户使用其 Azure AD 帐户自动登录到 Asana（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Asana 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Asana 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Asana 支持 **SP** 发起的 SSO

* Asana 支持[**自动**用户预配](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>从库中添加 Asana

要配置 Asana 与 Azure AD 的集成，需要将库中的 Asana 添加到托管的 SaaS 应用列表。

**若要从库中添加 Asana，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Asana”，在结果面板中选择“Asana”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Asana](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户配置和测试 Asana 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Asana 相关用户之间建立链接关系。

若要通过 Asana 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Asana 单一登录](#configure-asana-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Asana 测试用户](#create-asana-test-user)** - 在 Asana 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Asana 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Asana”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Asana 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”  文本框中，键入 URL：`https://app.asana.com/`

    b. 在“标识符(实体 ID)”  文本框中，键入 URL：`https://app.asana.com/`

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Asana”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-asana-single-sign-on"></a>配置 Asana 单一登录

1. 在其他浏览器窗口中，登录 Asana 应用程序。 若要在 Asana 中配置 SSO，请单击屏幕右上角的工作区名称，访问工作区设置。 然后，单击“\<工作区名称\> 设置”。

    ![Asana SSO 设置](./media/asana-tutorial/tutorial_asana_09.png)

2. 在“组织设置”  窗口中，单击“管理”  。 然后，单击“成员必须通过 SAML 登录”，启用 SSO 配置  。 然后执行以下步骤：

    ![配置单一登录组织设置](./media/asana-tutorial/tutorial_asana_10.png)  

    a. 在“登录页 URL”文本框中，粘贴“登录 URL”。  

    b. 右键单击从 Azure 门户下载的证书，然后使用记事本或首选文本编辑器打开证书文件。 复制证书开始和结束标题之间的内容，并将其粘贴在“X.509 证书”  文本框中。

3. 单击“保存”  。 如果需要进一步的协助，请转到 [“设置 SSO 的 Asana 指南”](https://asana.com/guide/help/premium/authentication#gl-saml)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Asana 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”  、“所有应用程序”  和“Asana”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“Asana”  。

    ![应用程序列表中的 Asana 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-asana-test-user"></a>创建 Asana 测试用户

本部分的目的是在 Asana 中创建名为“Britta Simon”的用户。 Asana 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](asana-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

在本部分中，创建 Asana 中名为“Britta Simon”的用户。

1. 在“Asana”  上，转到左侧窗格上的“团队”  部分。 单击加号按钮。

    ![创建 Azure AD 测试用户](./media/asana-tutorial/tutorial_asana_12.png)

2. 在文本框中键入该用户的电子邮件（例如 britta.simon\@contoso.com），然后选择“邀请”。

3. 单击“发送邀请”  。 新用户的电子邮件帐户中会收到一封电子邮件。 该用户需要创建并验证该帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Asana 磁贴时，应会自动登录到为其设置了 SSO 的 Asana。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](asana-provisioning-tutorial.md)
