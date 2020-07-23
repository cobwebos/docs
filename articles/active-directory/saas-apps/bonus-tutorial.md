---
title: 教程：Azure Active Directory 与 Bonusly 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Bonusly 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232065"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>教程：Azure Active Directory 与 Bonusly 集成

本教程介绍了如何将 Bonusly 与 Azure Active Directory (Azure AD) 集成。
将 Bonusly 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Bonusly。
* 可以让用户使用其 Azure AD 帐户自动登录到 Bonusly（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Bonusly 的集成，需具备以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Bonusly 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Bonusly 支持 **IDP** 发起的 SSO

## <a name="adding-bonusly-from-the-gallery"></a>从库中添加 Bonusly

要配置 Bonusly 与 Azure AD 的集成，需要从库中将 Bonusly 添加到托管 SaaS 应用列表。

**若要从库中添加 Bonusly，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Bonusly”，在结果面板中选择“Bonusly”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Bonusly](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”  的测试用户配置和测试 Bonusly 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Bonusly 相关用户之间建立链接关系。

若要配置和测试 Bonusly 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Bonusly 单一登录](#configure-bonusly-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Bonusly 测试用户](#create-bonusly-test-user)** - 在 Bonusly 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Bonusly 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 **Bonusly** 应用程序集成页上，选择“单一登录”。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Bonusly 域和 URL 单一登录信息](common/idp-reply.png)

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Bonusly 客户端支持团队](https://bonus.ly/contact)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制**指纹**并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 Bonusly”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-bonusly-single-sign-on"></a>配置 Bonusly 单一登录

1. 在另一个浏览器窗口中，登录到 **Bonusly** 租户。

1. 在顶部工具栏中，单击“设置”，并选择“集成和应用”。  

    ![“Bonusly 社交”部分](./media/bonus-tutorial/ic773686.png "Bonusly")
1. 在“单一登录”下选择“SAML”。  

1. 在“SAML”对话框页上执行以下步骤： 

    ![“Bonusly Saml”对话框页](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. 在“IdP SSO 目标 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在“IdP 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“IdP 颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。
    
    d. 将从 Azure 门户复制的“指纹”值粘贴到“证书指纹”文本框中   。
    
    e. 单击“保存”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Bonusly 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Bonusly”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Bonusly”  。

    ![应用程序列表中的 Bonusly 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-bonusly-test-user"></a>创建 Bonusly 测试用户

为了使 Azure AD 用户能够登录 Bonusly，必须将其预配到 Bonusly 中。 就 Bonusly 来说，预配任务需要手动完成。

> [!NOTE]
> 可使用其他任何 Bonusly 用户帐户创建工具或 Bonusly 提供的 API 预配 Azure AD 用户帐户。 

**若要配置用户设置，请执行以下步骤：**

1. 在 Web 浏览器窗口中，登录到 Bonusly 租户。

1. 单击“设置”  。

    ![设置](./media/bonus-tutorial/ic781041.png "设置")

1. 单击“用户和奖金”  选项卡。

    ![用户和奖金](./media/bonus-tutorial/ic781042.png "用户和奖金")

1. 单击“管理用户”。 

    ![管理用户](./media/bonus-tutorial/ic781043.png "管理用户")

1. 单击“添加用户”  。

    ![添加用户](./media/bonus-tutorial/ic781044.png "添加用户")

1. 在“添加用户”对话框中，执行以下步骤： 

    ![添加用户](./media/bonus-tutorial/ic781045.png "添加用户")  

    a. 在“名字”文本框中，输入用户的名字（如“Britta”）   。

    b. 在“姓氏”文本框中，输入用户的姓氏（如“Simon”）   。

    c. 在“电子邮件”文本框中，输入用户的电子邮件地址（例如 `brittasimon\@contoso.com`）。

    d. 单击“保存”  。

    > [!NOTE]
    > Azure AD 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。  

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Bonusly 磁贴时，应会自动登录到已为其设置了 SSO 的 Bonusly。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
