---
title: 教程：Azure Active Directory 与 AnswerHub 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AnswerHub 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73152988"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>教程：Azure Active Directory 与 AnswerHub 集成

本教程介绍如何将 AnswerHub 与 Azure Active Directory (Azure AD) 进行集成。
将 AnswerHub 与 Azure AD 集成具有以下优势：

* 可以使用 Azure AD 控制有权限访问 AnswerHub 的人员。
* 可让用户使用其 Azure AD 帐户自动登录到 AnswerHub（单一登录）。
* 可在一个中心位置（即 Azure 经典门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 AnswerHub 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以先使用[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 启用了单一登录的 AnswerHub 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* AnswerHub 支持 SP 发起的 SSO。

## <a name="add-answerhub-from-the-gallery"></a>从库中添加 AnswerHub

若要设置 AnswerHub 与 Azure AD 的集成，需要将库中的 AnswerHub 添加到托管的 SaaS 应用程序。

从库中添加 AnswerHub： 

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加应用程序，请在窗口顶部选择“新建应用程序”  。

    ![“新建应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 AnswerHub  。 在结果列表中选择“AnswerHub”，然后选择“添加”   。

     ![结果列表中的“AnswerHub”](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>设置和测试 Azure AD 单一登录

在本部分，我们使用名为 Britta Simon 的测试用户来配置并测试 AnswerHub 的 Azure AD 单一登录。
对于单一登录，需要建立 Azure AD 用户与 AnswerHub 中对应用户之间的关联。

若要配置和测试 AnswerHub 的 Azure AD 单一登录，需要完成以下任务：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [配置 AnswerHub 单一登录](#configure-answerhub-single-sign-on)，从而在应用程序端配置单一登录设置。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，Britta Simon。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. 创建 AnswerHub 测试用户，该用户需对应且关联到 Azure AD 测试用户。
6. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在此部分中，设置 Azure 门户中的 Azure AD 单一登录。

配置 AnswerHub 的 Azure AD 单一登录： 

1. 在 [Azure 门户](https://portal.azure.com/)中的“AnswerHub”应用程序集成页上，选择“单一登录”。  

    ![“单一登录”按钮](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录。  

    ![“单一登录选择方法”对话框](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择编辑图标打开“基本 SAML 配置”对话框。  

    ![“设置 SAML 单一登录”页](common/edit-urls.png)

4. 在“基本 SAML 配置”部分完成以下步骤： 

    ![“基本 SAML 配置”部分](common/sp-identifier.png)

    a. 在“登录 URL”框中，输入以下模式的 URL：`https://<company>.answerhub.com`

    b. 在“标识符(实体 ID)”框中，输入以下模式的 URL：`https://<company>.answerhub.com`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 若要获取该值，请与 [AnswerHub 支持团队](mailto:success@answerhub.com)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，选择“证书(Base64)”旁的“下载”链接（根据要求），并将证书保存在计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 AnswerHub”部分，根据要求复制相应的一个或多个 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

   可以复制以下 URL：
    - 登录 URL

    - Azure AD 标识符

    - 注销 URL

### <a name="configure-answerhub-single-sign-on"></a>配置 AnswerHub 单一登录

本部分将设置 AnswerHub 单一登录。  

配置 AnswerHub 单一登录： 

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 AnswerHub 公司网站。

    > [!NOTE]
    > 如果配置 AnswerHub 时需要帮助，请联系 [AnswerHub 支持团队](mailto:success@answerhub.com.)。

2. 转到“管理”  。

3. 在“用户和组”选项卡的左侧窗格中，选择“社交设置”部分中的“SAML 设置”    。

4. 在“IDP 配置”选项卡上，完成以下步骤  ：

    ![“用户和组”选项卡](./media/answerhub-tutorial/ic785172.png "SAML 设置")  
  
    a. 在“IDP 登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”   。
  
    b. 在“IDP 注销 URL”框中，粘贴从 Azure 门户复制的“注销 URL”   。

    c. 在“IDP 名称标识符格式”框中，输入在 Azure 门户的“用户属性”部分中选定的“标识符”值    。
  
    d. 选择“密钥和证书”  。

5. 在“密钥和证书”部分中，完成以下步骤  ：

    ![密钥和证书部分](./media/answerhub-tutorial/ic785173.png "密钥和证书")  

    a. 在记事本中打开从 Azure 门户下载的 Base64 编码证书，复制其内容，然后粘贴到“IDP 公钥(x509 格式)”框中  。
  
    b. 选择“保存”。 

6. 在“IDP 配置”选项卡中，再次选择“保存”   。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

创建 Azure AD 测试用户： 

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![依次选择“Azure Active Directory”、“用户”、“所有用户”](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在用户属性中，完成以下步骤。

    ![用户属性](common/user-properties.png)

    a. 在“姓名”  框中，输入 **BrittaSimon**。
  
    b. 在“用户名”框中，输入“brittasimon\@<yourcompanydomain.extension>”。  
    例如，BrittaSimon@contoso.com 。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

    d. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户 Britta Simon 访问 AnswerHub 的权限，将其设置为使用 Azure AD 单一登录。

分配 Azure AD 测试用户： 

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“AnswerHub”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“AnswerHub”  。

    ![“应用程序”列表](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![选择“用户和组”](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后选择屏幕底部的“选择”按钮。    

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  

7. 选择屏幕底部的“选择”按钮。 

8. 在“添加分配”对话框中选择“分配”。  

### <a name="create-an-answerhub-test-user"></a>创建 AnswerHub 测试用户

若要使 Azure AD 用户能够登录到 AnswerHub，需要将其添加到 AnswerHub 中。 在 AnswerHub 中，手动执行此任务。

设置用户帐户： 

1. 以管理员身份登录 AnswerHub 公司站点  。

2. 转到“管理”  。

3. 选择“用户和组”选项卡  。

4. 在左侧窗格的“管理用户”部分中，选择“创建或导入用户”，然后选择“用户和组”    。

   ![“用户和组”选项卡](./media/answerhub-tutorial/ic785175.png "用户和组")

5. 在相应的框中，输入要添加的有效 Azure AD 帐户的“电子邮件地址”、“用户名”和“密码”，然后选择“保存”     。

> [!NOTE]
> 可使用 AnswerHub 提供的其他任何用户帐户创建工具或 API 来设置 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“AnswerHub”磁贴时，应会自动登录到设置了 SSO 的 AnswerHub。 有关访问面板的详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

