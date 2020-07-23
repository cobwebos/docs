---
title: 教程：Azure Active Directory 与 Adobe Sign 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adobe Sign 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154019"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>教程：Azure Active Directory 与 Adobe Sign 集成

在本教程中，了解如何将 Adobe Sign 与 Azure Active Directory (Azure AD) 集成。
将 Adobe Sign 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Adobe Sign。
* 可以让用户使用其 Azure AD 帐户自动登录到 Adobe Sign（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Adobe Sign 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Adobe Sign 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Adobe Sign 支持 SP 发起的 SSO 

## <a name="adding-adobe-sign-from-the-gallery"></a>从库中添加 Adobe Sign

若要配置 Adobe Sign 与 Azure AD 的集成，需要从库中将 Adobe Sign 添加到托管 SaaS 应用列表。

**若要从库中添加 Adobe Sign，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Adobe Sign”，在结果面板中选择“Adobe Sign”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Adobe Sign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试 Adobe Sign 的 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 Adobe Sign 中的相关用户之间建立链接关系。

若要配置和测试 Adobe Sign 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Adobe Sign 单一登录](#configure-adobe-sign-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Adobe Sign 测试用户](#create-adobe-sign-test-user)** - 在 Adobe Sign 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Adobe Sign 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Adobe Sign 应用程序集成页上，选择“单一登录”  。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Adobe Sign 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com/`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Adobe Sign”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-adobe-sign-single-sign-on"></a>配置 Adobe Sign 单一登录

1. 在配置之前，请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)，以便将域添加到 Adobe Sign 允许列表中。 下面介绍如何添加域：

    a. [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)会向你发送随机生成的令牌。 对于域，令牌将如下所示：**adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. 在 DNS 文本记录中发布该验证令牌并通知 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)。
    
    > [!NOTE]
    > 这可能需要几天或更长时间。 请注意，DNS 传播延迟意味着 DNS 中发布的值可能在一小时或更长时间内不可见。 IT 管理员应具备有关如何在 DNS 文本记录中发布此令牌的丰富知识。
    
    c. 发布令牌并通过支持票证通知[Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)后，他们会验证该域并将其添加到你的帐户。
    
    d. 一般而言，在 DNS 记录中发布令牌的方法如下：

    * 登录到域帐户
    * 查找用于更新 DNS 记录的页面。 此页面可能名为“DNS 管理”、“名称服务器管理”或“高级设置”。
    * 查找你的域的 TXT 记录。
    * 添加带有 Adobe 提供的完整令牌值的 TXT 记录。
    * 保存所做更改。

1. 在其他 Web 浏览器窗口中，以管理员身份登录到 Adobe Sign 公司站点。

1. 在 SAML 菜单中，选择“帐户设置”   >   “SAML 设置”。
   
    ![Adobe Sign SAML 设置页的屏幕截图](./media/adobe-echosign-tutorial/ic789520.png "帐户")

1. 在“SAML 设置”部分执行以下步骤  ：
  
   ![SAML 设置的屏幕截图](./media/adobe-echosign-tutorial/ic789521.png "SAML 设置")
   
   ![SAML 设置的屏幕截图](./media/adobe-echosign-tutorial/ic789522.png "SAML 设置")

   a. 在“SAML 模式”下  ，选择“SAML 强制”  。
   
   b. 选择“允许 Echosign 帐户管理员使用 Echosign 凭据登录”  。
   
   c. 在“用户创建”下  ，选择“自动添加通过 SAML 验证了身份的用户”  。

   d. 将从 Azure 门户复制的“Azure AD 标识符”粘贴到“Idp 实体 ID”文本框   。
    
   e. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“Idp 登录 URL”   。
   
   f. 在“Idp 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。

   g. 在记事本中打开已下载的 **Certificate(Base64)** 文件。 将其内容复制到剪贴板，然后将其粘贴到“IdP 证书”  文本框。

   h. 选择“保存更改”。 

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

在本部分中，通过向 Britta Simon 授予 Adobe Sign 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Adobe Sign”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Adobe Sign”  。

    ![应用程序列表中的 Adobe Sign 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-adobe-sign-test-user"></a>创建 Adobe Sign 测试用户

为了使 Azure AD 用户能够登录到 Adobe Sign，必须对其进行预配。 这是一个手动任务。

>[!NOTE]
>可以使用任何其他 Adobe Sign 用户帐户创建工具或 Adobe Sign 提供的 API 预配 Azure AD 用户帐户。 

1. 以管理员身份登录到 **Adobe Sign** 公司站点。

2. 在顶部菜单中，选择“帐户”。  然后，在左窗格中选择“用户和组” > “创建新用户”。
   
    ![Adobe Sign 公司站点的屏幕截图，其中突出显示了“帐户”、“用户和组”和“创建新用户”](./media/adobe-echosign-tutorial/ic789524.png "帐户")
   
3. 在“创建新用户”  部分中，执行以下步骤：
   
    ![“创建新用户”部分的屏幕截图](./media/adobe-echosign-tutorial/ic789525.png "创建用户")
   
    a. 键入希望在相关文本框中预配的有效 Azure AD 帐户的“电子邮件地址”  、“名字”  和“姓氏”  。
   
    b. 选择“创建用户”  。

>[!NOTE]
>Azure Active Directory 帐户持有者收到一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Adobe Sign 磁贴时，应会自动登录到为其设置了 SSO 的 Adobe Sign。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

