---
title: 教程：Azure Active Directory 与 Zscaler Beta 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Beta 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: jeedes
ms.openlocfilehash: 731d99c84c9d34328ba7cc624610714c58ed00e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825385"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>教程：Azure Active Directory 与 Zscaler Beta 的集成

本教程介绍如何将 Zscaler Beta 与 Azure Active Directory (Azure AD) 集成。
将 Zscaler Beta 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Zscaler Beta。
* 可让用户使用其 Azure AD 帐户自动登录到 Zscaler Beta（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler Beta 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Zscaler Beta 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zscaler Beta 支持 SP 发起的 SSO
* Zscaler Beta 支持“恰时”用户预配

## <a name="adding-zscaler-beta-from-the-gallery"></a>从库中添加 Zscaler Beta

若要配置 Zscaler Beta 与 Azure AD 的集成，需从库中将 Zscaler Beta 添加到托管 SaaS 应用列表。

若要从库中添加 Zscaler Beta，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zscaler Beta”，在结果面板中选择“Zscaler Beta”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Zscaler Beta](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，将基于名为“Britta Simon”的测试用户配置并测试 Zscaler Beta 的 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 Zscaler Beta 中的相关用户之间建立链接关系。

若要配置和测试 Zscaler Beta 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zscaler Beta 单一登录](#configure-zscaler-beta-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Zscaler Beta 测试用户](#create-zscaler-beta-test-user) - 在 Zscaler Beta 中创建 Britta Simon 的对应用户，其与用户的 Azure AD 表示形式相关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Zscaler Beta 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Zscaler Beta 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Zscaler Beta 域和 URL 单一登录信息](common/sp-intiated.png)

    在“登录 URL”文本框中，键入用户用来登录 Zscaler Beta 应用程序的 URL。

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Zscaler Beta 客户端支持团队](https://www.zscaler.com/company/contact)获取此值。

5. Zscaler Beta 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 名称 | 源属性 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

7. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 Zscaler Beta”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-zscaler-beta-single-sign-on"></a>配置 Zscaler Beta 单一登录

1. 在其他 Web 浏览器窗口中，以管理员身份登录 Zscaler Beta 公司站点。

2. 转到“管理”>“身份验证”>“身份验证设置”并执行以下步骤：
   
    ![管理](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. 在“身份验证类型”下选择“SAML”。

    b. 单击“配置 SAML”。

3. 在“编辑 SAML”窗口中，执行以下步骤并单击“保存”。  
            
    ![管理用户和身份验证](./media/zscaler-beta-tutorial/ic800208.png "管理用户和身份验证")
    
    a. 在“SAML 门户 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”。

    b. 在“登录名属性”文本框中，输入 **NameID**。

    c. 单击“上传”，以上传从 Azure 门户的“公共 SSL 证书”中下载的 Azure SAML 签名证书。

    d. 切换“启用 SAML 自动预配”。

    e. 若要为 displayName 属性启用 SAML 自动预配，请在“用户显示名称属性”文本框中输入 **displayName**。

    f. 若要为 memberOf 属性启用 SAML 自动预配，请在“组名称属性”文本框中输入 **memberOf**。

    g. 若要为 department 属性启用 SAML 自动预配，请在“部门名称属性”中输入 **department**。

    h. 单击“ **保存**”。

4. 在“配置用户身份验证”对话框页上，执行以下步骤：

    ![管理](./media/zscaler-beta-tutorial/ic800207.png)

    a. 将鼠标悬停在左下角附近的“激活”菜单上。

    b. 单击“激活”。

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。   
    
     ![Internet 选项](./media/zscaler-beta-tutorial/ic769492.png "Internet 选项")

3. 单击“连接”选项卡。   
  
     ![连接](./media/zscaler-beta-tutorial/ic769493.png "连接")

4. 单击“LAN 设置”，打开“LAN 设置”对话框。

5. 在“代理服务器”部分中，执行以下步骤：   
   
    ![代理服务器](./media/zscaler-beta-tutorial/ic769494.png "代理服务器")

    a. 选择“为 LAN 使用代理服务器”。

    b. 在“地址”文本框中，键入 **gateway.Zscaler Beta.net**。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”。

    e. 单击“确定”，关闭“局域网(LAN)设置”对话框。

6. 单击“确定”，关闭“Internet 选项”对话框。

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

本部分中，通过授予 Britta Simon 访问 Zscaler Beta 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Zscaler Beta”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Zscaler Beta”。

    ![应用程序列表中的 Zscaler Beta 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，从列表中选择用户（例如“Britta Simon”），然后单击屏幕底部的“选择”按钮。

    ![图像](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. 从“选择角色”对话框中，选择列表中合适的用户角色，然后单击屏幕底部的“选择”按钮。

    ![图像](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. 在“添加分配”对话框中，选择“分配”按钮。

    ![图像](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-zscaler-beta-test-user"></a>创建 Zscaler Beta 测试用户

在本部分，我们将在 Zscaler Beta 中创建一个名为 Britta Simon 的用户。 Zscaler Beta 支持默认启用的**恰时用户预配**。 此部分不存在任何操作项。 如果 Zscaler Beta 中尚不存在用户，则会在身份验证后创建一个新用户。

>[!Note]
>如需手动创建用户，请联系  [Zscaler Beta 支持团队](https://www.zscaler.com/company/contact)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Zscaler Beta 磁贴时，应当会自动登录到为其设置了 SSO 的 Zscaler Beta。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

