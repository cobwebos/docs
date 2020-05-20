---
title: 教程：Azure Active Directory 与 BitaBIZ 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BitaBIZ 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159391"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>教程：Azure Active Directory 与 BitaBIZ 集成

在本教程中，了解如何将 BitaBIZ 与 Azure Active Directory (Azure AD) 集成。
将 BitaBIZ 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 BitaBIZ
* 可以让用户使用其 Azure AD 帐户自动登录到 BitaBIZ（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 BitaBIZ 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 BitaBIZ 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* BitaBIZ 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-bitabiz-from-the-gallery"></a>从库中添加 BitaBIZ

若要配置 BitaBIZ 与 Azure AD 的集成，需要将库中的 BitaBIZ 添加到托管的 SaaS 应用列表。

**若要从库中添加 BitaBIZ，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“BitaBIZ”，在结果面板中选择“BitaBIZ”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 BitaBIZ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 BitaBIZ 配置和测试 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 BitaBIZ 相关用户之间建立链接关系。

若要配置和测试 BitaBIZ 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 BitaBIZ 单一登录](#configure-bitabiz-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 BitaBIZ 测试用户](#create-bitabiz-test-user)** - 在 BitaBIZ 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 BitaBIZ 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 **BitaBIZ** 应用程序集成页上，选择“单一登录”。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![BitaBIZ 域和 URL 单一登录信息](common/idp-identifier.png)

    在“标识符”文本框中，使用以下模式键入 URL：`https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > 上述 URL 中的值仅供演示。 本教程稍后会介绍如何使用实际标识符更新该值。

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![image](common/both-preintegrated-signon.png)

    在“登录 URL”文本框中，键入 URL：  `https://www.bitabiz.com/dashboard`

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 BitaBIZ”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-bitabiz-single-sign-on"></a>配置 BitaBIZ 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 BitaBIZ 租户。

2. 单击“设置管理员”。 

    ![BitaBIZ 配置](./media/bitabiz-tutorial/settings1.png)

3. 单击“添加值”部分下面的“Microsoft 集成”。  

    ![BitaBIZ 配置](./media/bitabiz-tutorial/settings2.png)

4. 向下滚动到“Microsoft Azure AD (启用单一登录)”部分并执行以下步骤： 

    ![BitaBIZ 配置](./media/bitabiz-tutorial/settings3.png)

    a. 复制“实体 ID (Azure AD 中的‘标识符’)”文本框中的值，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分中的“标识符”文本框。    

    b. 在“Azure AD 单一登录服务 URL”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  。

    c. 在“Azure AD SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中打开下载的“证书(Base64)”文件，将其内容复制到剪贴板，然后将其粘贴到“Azure AD 签名证书(Base64 编码)”文本框中   。

    e. 将业务电子邮件域名（即 mycompany.com）添加到“域名”文本框中，以使用此电子邮件域将 SSO 分配给公司中的用户（不是必需的）。 

    f. 为 BitaBIZ 帐户标记“已启用 SSO”。 

    g. 单击“保存 Azure AD 配置”，以保存并激活 SSO 配置。 

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

在本部分中，通过授予 Britta Simon 访问 BitaBIZ 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“BitaBIZ”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“BitaBIZ”  。

    ![应用程序列表中的 BitaBIZ 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-bitabiz-test-user"></a>创建 BitaBIZ 测试用户

为了使 Azure AD 用户登录到 BitaBIZ，必须将其预配到 BitaBIZ。  
就 BitaBIZ 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 BitaBIZ 公司站点。

2. 单击“设置管理员”。 

    ![BitaBIZ - 添加用户](./media/bitabiz-tutorial/settings1.png)

3. 单击“组织”部分下面的“添加用户”。  

    ![BitaBIZ - 添加用户](./media/bitabiz-tutorial/user1.png)

4. 单击“添加新员工”。 

    ![BitaBIZ - 添加用户](./media/bitabiz-tutorial/user2.png)

5. 在“添加新员工”  对话框页上，执行以下步骤：

    ![BitaBIZ - 添加用户](./media/bitabiz-tutorial/user3.png)

    a. 在“名字”  文本框中，键入用户的名字（如“Britta”）。

    b. 在“姓氏”  文本框中，键入用户的姓氏（如“Simon”）。

    c. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“雇用日期”中选择一个日期。 

    e. 还可以针对用户设置其他非必需的用户属性。 有关详细信息，请参阅[员工设置文档](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)。

    f. 单击“保存员工”。 

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 BitaBIZ 磁贴时，应当会自动登录到已为其设置了 SSO 的 BitaBIZ。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
