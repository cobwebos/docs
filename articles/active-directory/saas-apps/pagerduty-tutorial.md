---
title: 教程：Azure Active Directory 与 PagerDuty 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 PagerDuty 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 5470c13f75d010634f97e87dc1a870a100187973
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835061"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>教程：Azure Active Directory 与 PagerDuty 集成

在本教程中，了解如何将 PagerDuty 与 Azure Active Directory (Azure AD) 集成。
将 PagerDuty 与 Azure AD 集成提供以下优势：

* 您可以控制有权访问 PagerDuty 的 Azure AD 中。
* 可以让用户自动登录到 PagerDuty （单一登录） 使用其 Azure AD 帐户。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 PagerDuty 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* PagerDuty 上单一登录已启用的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* PagerDuty 支持**SP**启动的 SSO

## <a name="adding-pagerduty-from-the-gallery"></a>从库添加 PagerDuty

若要配置 PagerDuty 与 Azure AD 的集成，需要从库中将 PagerDuty 添加到托管 SaaS 应用列表。

**若要从库中添加 PagerDuty，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入**PagerDuty**，选择**PagerDuty**结果面板中单击**添加**按钮添加该应用程序。

     ![结果列表中的 PagerDuty](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，配置和测试 Azure AD 单一登录与基于调用的测试用户的 PagerDuty **Britta Simon**。
为实现单一登录工作，需要建立 Azure AD 用户与 PagerDuty 中的相关的用户之间的链接关系。

若要配置和测试 PagerDuty 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 PagerDuty 单一登录](#configure-pagerduty-single-sign-on)** -若要在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 PagerDuty 测试用户](#create-pagerduty-test-user)** -Britta Simon 的对应在 PagerDuty 链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Azure AD 单一登录 PagerDuty，请执行以下步骤：

1. 在中[Azure 门户](https://portal.azure.com/)，然后在**PagerDuty**应用程序集成页上，选择**单一登录**。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![PagerDuty 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [PagerDuty 客户端支持团队](https://www.pagerduty.com/support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 上**设置 PagerDuty**部分中，复制具体请根据要求的相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-pagerduty-single-sign-on"></a>PagerDuty 单一登录配置

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Pagerduty 公司站点。

2. 在顶部菜单中，单击“帐户设置”。

    ![帐户设置](./media/pagerduty-tutorial/ic778535.png "帐户设置")

3. 单击“单一登录”。

    ![单一登录](./media/pagerduty-tutorial/ic778536.png "单一登录")

4. 在“启用单一登录(SSO)”页上，执行以下步骤：

    ![启用单一登录](./media/pagerduty-tutorial/ic778537.png "启用单一登录")

    a. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“X.509 证书”文本框中
  
    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”。
  
    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”。

    d. 选择“允许用户名/密码登录”。

    e. 选中“需要精确的身份验证上下文比较”复选框。

    f. 单击“保存更改”。

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
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 PagerDuty 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择**企业应用程序**，选择**的所有应用程序**，然后选择**PagerDuty**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“PagerDuty”。

    ![应用程序列表中的 PagerDuty 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-pagerduty-test-user"></a>创建 PagerDuty 测试用户

若要让 Azure AD 用户登录 PagerDuty，必须将其预配到 PagerDuty 中。  
使用 PagerDuty 时，预配属手动任务。

>[!NOTE]
>可以使用任何其他 PagerDuty 用户帐户创建工具或 PagerDuty 提供的 API 来预配 Azure Active Directory 用户帐户。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Pagerduty** 租户。

2. 在顶部菜单中，单击“用户”。

3. 单击“添加用户”。
   
    ![添加用户](./media/pagerduty-tutorial/ic778539.png "添加用户")

4.  在“邀请团队”对话框中，执行以下步骤：
   
    ![邀请团队](./media/pagerduty-tutorial/ic778540.png "邀请团队")

    a. 键入用户的**名字和姓氏**，例如 **Britta Simon**。 
   
    b. 输入**电子邮件**地址的用户喜欢**brittasimon\@contoso.com**。
   
    c. 依次单击“添加”、“发送邀请”。
   
    >[!NOTE]
    >所有已添加的用户都会收到创建 PagerDuty 帐户的邀请。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 PagerDuty 磁贴时，你应自动登录到 PagerDuty 设置 SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

