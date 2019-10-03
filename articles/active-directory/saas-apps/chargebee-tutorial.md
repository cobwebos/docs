---
title: 教程：Azure Active Directory 与 Chargebee 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Chargebee 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 033d413d-1656-4d9c-a606-dd33c23948f9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbaf3d527ad1e58914c6b3f9c8b5b4ea57ae08
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931847"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>教程：将 Chargebee 与 Azure Active Directory 集成

本教程介绍如何将 Chargebee 与 Azure Active Directory (Azure AD) 集成。 将 Chargebee 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Chargebee。
* 让用户使用其 Azure AD 帐户自动登录到 Chargebee。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Chargebee 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Chargebee 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-chargebee-from-the-gallery"></a>从库中添加 Chargebee

若要配置 Chargebee 与 Azure AD 的集成，需要从库中将 Chargebee 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Chargebee**。 
1. 在结果面板中选择“Chargebee”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>配置并测试 Chargebee 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Chargebee 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Chargebee 中的相关用户之间建立链接关系。

若要配置并测试 Chargebee 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Chargebee SSO](#configure-chargebee-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Chargebee 测试用户](#create-chargebee-test-user)** - 在 Chargebee 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Chargebee”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<domainname>.chargebee.com`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://app.chargebee.com/saml/<domainname>/acs`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` 是用户在声明帐户后创建的域的名称。 如需任何其他信息，请联系 [Chargebee 客户端支持团队](mailto:support@chargebee.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Chargebee”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Chargebee 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Chargebee”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-chargebee-sso"></a>配置 Chargebee SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Chargebee 公司站点。

4. 在菜单左侧，单击“设置” > “安全性” > “管理”。   

    ![Chargebee 配置](./media/chargebee-tutorial/config01.png)

5. 在“单一登录”弹出窗口中执行以下步骤： 

    ![Chargebee 配置](./media/chargebee-tutorial/config02.png)

    a. 选择“SAML”。 

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在记事本中打开 Base64 编码的证书，复制其内容，然后将其粘贴到“SAML 证书”文本框中  。

    d. 单击“确认”  。

### <a name="create-chargebee-test-user"></a>创建 Chargebee 测试用户

必须将 Azure AD 用户预配到 Chargebee 中，然后他们才能登录到 Chargebee。 在 Chargebee 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Chargebee。

2. 在菜单左侧单击“客户”，然后导航到“创建新客户”。  

    ![Freedcamp 配置](./media/chargebee-tutorial/config03.png)

3. 在“新建客户”页上，填写下面所示的相应字段，然后单击“创建客户”以创建用户。  

    ![Freedcamp 配置](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Chargebee”磁贴时，应会自动登录到设置了 SSO 的 Chargebee。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

