---
title: 教程：Azure Active Directory 与 CorpTax 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 CorpTax 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 1949e8fc648aac5772c12d2c38e233dc53ca8b17
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797059"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>教程：Azure Active Directory 与 CorpTax 集成

本教程介绍如何将 CorpTax 与 Azure Active Directory (Azure AD) 集成。
将 CorpTax 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 CorpTax。
* 可以让用户使用其 Azure AD 帐户自动登录到 CorpTax（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 CorpTax 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 CorpTax 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* CorpTax 支持 SP 发起的 SSO

## <a name="adding-corptax-from-the-gallery"></a>从库中添加 CorpTax

若要配置 CorpTax 与 Azure AD 的集成，需要从库中将 CorpTax 添加到托管 SaaS 应用列表。

**若要从库中添加 CorpTax，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select_azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add_new_app.png)

4. 在搜索框中，键入“CorpTax”，在结果面板中选择“CorpTax”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 CorpTax](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 CorpTax 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 CorpTax 相关用户之间建立链接关系。

若要配置和测试 CorpTax 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 CorpTax 单一登录](#configure-corptax-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. [创建 CorpTax 测试用户](#create-corptax-test-user) - 在 CorpTax 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
5. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 CorpTax 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在“CorpTax”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select_sso.png)

2. 在“选择单一登录方法”对话框中，选择 SAML/WS-Fed 模式以启用单一登录。

    ![单一登录选择模式](common/select_saml_option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit_urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![CorpTax 域和 URL 单一登录信息](common/sp_intiated.png)

    在“登录 URL”文本框中，键入 URL：`https://asp.corptax.com`

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>配置 CorpTax 单一登录

若要在 CorpTax 端配置单一登录，需将下载的“联合元数据 XML”发送给 [CorpTax 支持团队](mailto:bgraves@corptax.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new_user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user_properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。

    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 CorpTax 的访问权限支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“CorpTax”。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

2. 在应用程序列表中，键入并选择“CorpTax”。

    ![应用程序列表中的 CorpTax 链接](common/all_applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users_groups_blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add_assign_user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-corptax-test-user"></a>创建 CorpTax 测试用户

在本部分中，将在 CorpTax 中创建一个名为 Britta Simon 的用户。 请与  [CorpTax 支持团队](mailto:bgraves@corptax.com)协作，将用户添加到 CorpTax 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。
单击访问面板中的 CorpTax 磁贴时，应重定向到以下 CorpTax 页面- 

![图像](media/corptax-tutorial/corptaxlogin.png)

在“环境”文本框中键入相应环境，然后应自动登录到为其设置 SSO 的 CorpTax。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
