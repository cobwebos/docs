---
title: 教程：Azure Active Directory 与 FreshDesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FreshDesk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 71c1bea58f17b457f417a5b050640d04d44019d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551041"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 FreshDesk 集成

在本教程中，了解如何将 FreshDesk 与 Azure Active Directory (Azure AD) 集成。
将 FreshDesk 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 FreshDesk。
* 可以让用户使用其 Azure AD 帐户自动登录到 FreshDesk（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 FreshDesk 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 FreshDesk 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* FreshDesk 支持 **SP** 发起的 SSO
* 配置 FreshDesk 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-freshdesk-from-the-gallery"></a>从库中添加 FreshDesk

要配置 FreshDesk 与 Azure AD 的集成，需要从库中将 FreshDesk 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“FreshDesk” 。
1. 从结果面板中选择“FreshDesk”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>配置并测试 FreshDesk 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 FreshDesk 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 FreshDesk 相关用户之间建立关联。

若要配置并测试 FreshDesk 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置 FreshDesk 单一登录](#configure-freshdesk-single-sign-on)** - 在应用程序端配置单一登录。
    1. **[创建 FreshDesk 测试用户](#create-freshdesk-test-user)** - 在 FreshDesk 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

1. 在 [Azure 门户](https://portal.azure.com/)中的“FreshDesk”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”旁边的编辑/铅笔图标以编辑设置。 

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请与 [FreshDesk 客户端支持团队](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. FreshDesk 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 下面的屏幕截图显示默认属性的列表，而“唯一用户标识符”与 user.userprincipalname 映射，但 FreshDesk 希望此声明与 user.mail 映射，因此需要通过单击“编辑”图标来编辑属性映射，然后更改属性映射。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”**** 页上，在“SAML 签名证书”**** 部分中，单击“下载”**** 以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 FreshDesk”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 FreshDesk 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“FreshDesk”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“FreshDesk”。

    ![应用程序列表中的 FreshDesk 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-freshdesk-single-sign-on"></a>配置 FreshDesk 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。

2. 选择“安全性”图标，在“安全性”部分中，执行以下步骤：

    ![单一登录](./media/freshdesk-tutorial/configure-1.png "单一登录")
  
    a. 对于“单一登录”，请选择“启用”。 

    b. 在“登录方法”中，选择“SAML SSO”。

    c. 在“IdP 提供的实体 ID”文本框中，粘贴从 Azure 门户复制的“实体 ID”值 。

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 在“签名选项”中，从下拉菜单中选择“仅限已签名的断言”。

    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    g. 在“安全证书”文本框中，粘贴你之前获得的“证书(Base64)”值。
  
    h. 单击“ **保存**”。

## <a name="create-freshdesk-test-user"></a>创建 FreshDesk 测试用户

为使 Azure AD 用户能够登录到 FreshDesk，必须将其预配到 FreshDesk。  
就 FreshDesk 而言，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Freshdesk”租户。

1. 在左侧菜单中，单击“管理”，然后在“常规设置”选项卡中单击“代理”。
  
    ![代理](./media/freshdesk-tutorial/create-user-1.png "代理")

1. 单击“新建代理”。

    ![新建代理](./media/freshdesk-tutorial/create-user-2.png "新建代理")

1. 在“代理信息”对话框中，输入必填字段，然后单击“创建代理”。

    ![代理信息](./media/freshdesk-tutorial/create-user-3.png "代理信息")

    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    >
    >[!NOTE]
    >可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来将 Azure AD 用户帐户预配到 FreshDesk。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 FreshDesk 磁贴时，应当会自动登录到已为其设置了 SSO 的 FreshDesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

