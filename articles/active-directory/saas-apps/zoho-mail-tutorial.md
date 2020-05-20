---
title: 教程：Azure Active Directory 与 Zoho 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zoho 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b5d0e52e927a44d5bce5314e724c1c753809ce0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231938"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>教程：Azure Active Directory 与 Zoho 的集成

本教程介绍了如何将 Zoho 与 Azure Active Directory (Azure AD) 进行集成。
将 Zoho 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Zoho。
* 可以让用户使用其 Azure AD 帐户自动登录到 Zoho（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Zoho 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用单一登录的 Zoho 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zoho 支持 SP 发起的 SSO 

## <a name="adding-zoho-from-the-gallery"></a>从库中添加 Zoho

若要配置 Zoho 与 Azure AD 的集成，需要从库中将 Zoho 添加到托管 SaaS 应用列表。

**若要从库中添加 Zoho，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zoho”，在结果面板中选择“Zoho”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Zoho](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Zoho 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Zoho 相关用户之间建立链接关系。

若要配置和测试 Zoho 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zoho 单一登录](#configure-zoho-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Zoho 测试用户](#create-zoho-test-user)** - 在 Zoho 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Zoho 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在“Zoho”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Zoho 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<company name>.zohomail.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Zoho 客户端支持团队](https://www.zoho.com/mail/contact.html)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Zoho”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-zoho-single-sign-on"></a>配置 Zoho 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoho Mail 公司站点。

2. 转到“控制面板”  。
   
    ![控制面板](./media/zoho-mail-tutorial/ic789607.png "控制面板")

3. 单击“SAML 身份验证”选项卡。 
   
    ![SAML 身份验证](./media/zoho-mail-tutorial/ic789608.png "SAML 身份验证")

4. 在“SAML 身份验证详细信息”  部分，执行以下步骤：
   
    ![SAML 身份验证详细信息](./media/zoho-mail-tutorial/ic789609.png "SAML 身份验证详细信息")
   
    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。
   
    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。
   
    c. 在“更改密码 URL”  文本框中，粘贴从 Azure 门户复制的“更改密码 URL”  值。
       
    d. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“公钥”  文本框中。
   
    e. 选择“RSA”，作为**算法**。
   
    f. 单击“确定”。 

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

在本部分中，通过授予 Britta Simon 访问 Zoho 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Zoho”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Zoho”  。

    ![应用程序列表中的 Zoho 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-zoho-test-user"></a>创建 Zoho 测试用户

要使 Azure AD 用户能够登录 Zoho Mail，必须将这些用户预配到 Zoho Mail 中。 需要在 Zoho Mail 中手动进行预配。

> [!NOTE]
> 可使用 Zoho Mail 提供的任何其他 Zoho Mail 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 **Zoho Mail** 公司站点。

1. 转到“控制面板”\>“邮件和文档”。

1. 转到“用户详细信息\>添加用户”。
   
    ![添加用户](./media/zoho-mail-tutorial/ic789611.png "添加用户")

1. 在“添加用户”  对话框中，执行以下步骤：
   
    ![添加用户](./media/zoho-mail-tutorial/ic789612.png "添加用户")
   
    a. 在“名字”  文本框中，键入用户的名字（如“Britta”  ）。

    b. 在“姓氏”  文本框中，键入用户的姓氏（如“Simon”  ）。

    c. 在“电子邮件 ID”文本框中，键入用户的电子邮件 ID，例如 brittasimon\@Contoso.com。

    d. 在“密码”文本框中，输入用户的密码。 
   
    e. 单击“确定”。   
      
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，内附一个激活帐户前进行确认的链接。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Zoho 磁贴时，应当会自动登录到为其设置了 SSO 的 Zoho。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

