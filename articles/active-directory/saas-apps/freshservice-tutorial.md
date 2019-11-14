---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Freshservice 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570622"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成

本教程介绍如何将 Freshservice 与 Azure Active Directory (Azure AD) 集成。 将 Freshservice 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Freshservice。
* 让用户使用其 Azure AD 帐户自动登录到 Freshservice。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Freshservice 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Freshservice 支持 **SP** 发起的 SSO

## <a name="adding-freshservice-from-the-gallery"></a>从库中添加 Freshservice

若要配置 Freshservice 与 Azure AD 的集成，需要从库中将 Freshservice 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Freshservice”   。
1. 从结果面板中选择“Freshservice”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>配置并测试 Freshservice 的 Azure AD 单一登录

使用名为“B.Simon”的测试用户为 Freshservice 配置和测试 Azure AD SSO  。 若要使 SSO 有效，需要在 Azure AD 用户与 Freshservice 相关用户之间建立关联。

若要配置和测试 Freshservice 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Freshservice SSO](#configure-freshservice-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Freshservice 测试用户](#create-freshservice-test-user)** - 在 Freshservice 中创建 B. Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Freshservice”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<democompany>.freshservice.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<democompany>.freshservice.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Freshservice 客户端支持团队](https://support.freshservice.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. Freshservice 要求使用 SHA-256 指纹来使 SSO 正常工作。 若要获取 SHA-256 指纹，请执行以下步骤：

    ![指纹](./media/freshservice-tutorial/ic790821.png "指纹")

    1. 在不同的 Web 浏览器中打开[链接](https://www.samltool.com/fingerprint.php)。

    1. 在记事本中打开下载的证书 (Base64) 文件，将其内容粘贴到“X.509 证书”文本框中。 

    1. 对于算法，请从下拉列表中选择“sha256”  。

    1. 单击“计算指纹”  。

    1. 单击复制图标以复制生成的“指纹”  并将其保存在计算机上。

1. 在“Azure 门户”上的  “设置 Freshservice”  部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 B.Simon 授予对 Freshservice 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Freshservice”  。
1. 在应用的概览页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-freshservice-sso"></a>配置 Freshservice SSO

1. 若要在“Freshservice”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Freshservice”会将你定向到 Freshservice 应用程序  。 在这里，提供管理员凭据登录到 Freshservice。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Freshservice，请打开新的 Web 浏览器窗口，以管理员身份登录 Freshservice 公司网站，并执行以下步骤：

4. 在顶部菜单中，单击“管理员”。 

    ![管理员](./media/freshservice-tutorial/ic790814.png "管理员")

5. 在“客户门户”中单击“安全”。  

    ![安全](./media/freshservice-tutorial/ic790815.png "安全")

6. 在“安全”  部分中，执行以下步骤：

    ![单一登录](./media/freshservice-tutorial/ic790816.png "单一登录")

    a. 切换“单一登录”  。

    b. 选择“SAML SSO”。 

    c. 在“SAML 登录 URL”文本框中，粘贴从 Azure 门户复制的**登录 URL** 值。 

    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    e. 在“安全证书指纹”  文本框中，粘贴你之前生成的“指纹”  值。

    f. 单击“保存” 

### <a name="create-freshservice-test-user"></a>创建 Freshservice 测试用户

若要使 Azure AD 用户能够登录 FreshService，必须将这些用户预配到 FreshService 中。 对于 FreshService，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **FreshService** 公司站点。

2. 在顶部菜单中，单击“管理员”。 

    ![管理员](./media/freshservice-tutorial/ic790814.png "管理员")

3. 在“用户管理”部分，单击“请求者”   。

    ![请求者](./media/freshservice-tutorial/ic790818.png "请求者")

4. 单击“新建申请者”。 

    ![新建请求者](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. 在“新建请求者”  部分中，执行以下步骤：

    ![新建请求者](./media/freshservice-tutorial/ic790820.png "新建请求者")  

    a. 在相关文本框中输入要预配的有效 Azure Active Directory 帐户的“名字”和“电子邮件”属性。  

    b. 单击“ **保存**”。

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接
    >  

> [!NOTE]
> 可以使用 FreshService 提供的任何其他 FreshService 用户帐户创建工具或 API 来预配 AAD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Freshservice 磁贴时，应当会自动登录到你为其设置了 SSO 的 Freshservice。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [试用 Freshservice 与 Azure AD](https://aad.portal.azure.com/)