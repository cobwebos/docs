---
title: 教程：Azure Active Directory 与 Dropbox for Business 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Dropbox for Business 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 615c10357e099b547008b128ca5f5a773ff1aa14
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158305"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>教程：将 Dropbox for Business 与 Azure Active Directory 集成

本教程介绍如何将 Dropbox for Business 与 Azure Active Directory (Azure AD) 集成。 将 Dropbox for Business 与 Azure AD 集成时，可以：

* 在 Azure AD 中控制谁有权访问 Dropbox for Business。
* 让用户使用其 Azure AD 帐户自动登录到 Dropbox for Business。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Dropbox for Business 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

* 本教程在测试环境中配置并测试 Azure AD SSO。 Dropbox for Business 支持 SP 发起的 SSO 

* Dropbox for Business 支持实时用户预配 

## <a name="adding-dropbox-for-business-from-the-gallery"></a>从库中添加 Dropbox for Business

若要配置 Dropbox for Business 与 Azure AD 的集成，需要从库中将 Dropbox for Business 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Dropbox for Business”   。
1. 从结果面板中选择“Dropbox for Business”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **Britta Simon** 的测试用户配置和测试 Dropbox for Business 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Dropbox for Business 相关用户之间建立关联。

若要配置和测试 Dropbox for Business 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Dropbox for Business SSO](#configure-dropbox-for-business-sso)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Dropbox for Business 测试用户](#create-dropbox-for-business-test-user)** - 在 Dropbox for Business 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Dropbox for Business”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://www.dropbox.com/sso/<id>` 

    b. 在“标识符(实体 ID)”文本框中，键入值：`Dropbox` 

    > [!NOTE]
    > 上面的登录 URL 值不是实际值。 需使用实际登录 URL 更新该值（本教程稍后将会介绍）。

1. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Dropbox for Business”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-dropbox-for-business-sso"></a>配置 Dropbox for Business SSO

1. 若要在 Dropbox for Business 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”。  

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Dropbox for Business”  会定向到 Dropbox for Business 应用程序。 在此处，请提供用于登录到 Dropbox for Business 的管理员凭据。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Dropbox for Business，请打开新的 Web 浏览器窗口，转到 Dropbox for Business 租户并登录。 然后执行以下步骤：

    ![配置单一登录](./media/dropboxforbusiness-tutorial/ic769509.png "配置单一登录")

4. 单击“用户”图标并选择“设置”选项卡。  

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure1.png "配置单一登录")

5. 在左侧导航窗格中，单击“管理控制台”  。

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure2.png "配置单一登录")

6. 在“管理控制台”  上，单击左侧导航窗格中的“设置”  。

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure3.png "配置单一登录")

7. 选择“身份验证”部分下的“单一登录”选项   。

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure4.png "配置单一登录")

8. 在“单一登录”部分中，执行以下步骤  ：  

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure5.png "配置单一登录")

    a. 在“单一登录”的下拉列表中选择“必选”选项   。

    b. 单击“添加单一 URL”，在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户中复制的“登录 URL”值，然后选择“完成”     。

    ![配置单一登录](./media/dropboxforbusiness-tutorial/configure6.png "配置单一登录")

    c. 单击“上传证书”，然后浏览至从 Azure 门户中下载的“Base64 编码的证书文件”   。

    d. 单击“复制链接”，并将复制的值粘贴到 Azure 门户上“Dropbox for Business 域和 URL”部分的“登录 URL”文本框中    。

    e. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Dropbox for Business 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Dropbox for Business”  。
1. 在应用的概览页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-dropbox-for-business-test-user"></a>创建 Dropbox for Business 测试用户

本部分将在 Dropbox for Business 中创建一个名为 Britta Simon 的用户。 Dropbox for Business 支持在默认情况下启用的实时用户预配。 此部分不存在任何操作项。 如果 Dropbox for Business 中尚不存在用户，则会在身份验证后创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Dropbox for Business 客户端支持团队](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 Dropbox for Business 磁贴时，应当会自动登录到你为其设置了 SSO 的 Dropbox for Business。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)