---
title: 教程：Azure Active Directory 与 Periscope Data 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Periscope Data 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 878d9b40172313ac6c3d816cbf0da6aba5e18542
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904149"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>教程：Azure Active Directory 与 Periscope Data 集成

在本教程中，了解如何将 Periscope Data 与 Azure Active Directory (Azure AD) 集成。
将 Periscope Data 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Periscope Data。
* 可以让用户使用其 Azure AD 帐户自动登录到 Periscope Data（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Periscope Data 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Periscope Data 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Periscope Data 支持 SP 发起的 SSO

## <a name="adding-periscope-data-from-the-gallery"></a>从库中添加 Periscope Data

若要配置 Periscope Data 与 Azure AD 的集成，需要从库中将 Periscope Data 添加到托管 SaaS 应用列表。

**若要从库中添加 Periscope Data，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Periscope Data”，在结果面板中选择“Periscope Data”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Periscope Data](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Periscope Data 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Periscope Data 相关用户之间建立链接关系。

若要配置和测试 Periscope Data 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Periscope Data 单一登录](#configure-periscope-data-single-sign-on)** - 在应用程序端配置“单一登录”设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Periscope Data 测试用户](#create-periscope-data-test-user)** - 在 Periscope Data 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Periscope Data 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Periscope Data 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Periscope Data 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，键入任何 URL：
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > “登录 URL”值不是实际值。 请将这些值更新为实际登录 URL。 与 [Periscope Data 客户端支持团队](mailto:support@periscopedata.com)获取此值，以及将从本教程后面部分说明的“配置 Periscope Data 单一登录”部分获取的标识符值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>配置 Periscope Data 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Periscope Data。

2. 打开在左下角的齿轮菜单并打开“帐单” > “安全”菜单，然后执行以下步骤。 只有管理员才有权访问这些设置。

    ![Periscope Data 配置信息](./media/periscope-data-tutorial/configure01.png)

    a. 从步骤 #5“SAML 签名证书”中复制“应用联合元数据 URL”，然后在浏览器中打开它。 这会打开一个 XML 文档。

    b. 在“单一登录”文本框中，选择“Azure Active Directory”。

    c. 查找标记 SingleSignOnService并在“SSO URL”文本框中粘贴“位置”值。

    d. 查找标记 SingleLogoutService并在“SLO URL”文本框中粘贴“位置”值。

    e. 复制实例的“标识符”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。

    f. 找到 XML 文件的第一个标记，复制 entityID 的值并将其粘贴到“颁发者”文本框中。

    g. 查找具有 SAML 协议的标记 IDPSSODescriptor。 在该节中，查找 use=signing 的标记 KeyDescriptor。 复制 X509Certificate 的值，并将其粘贴到“证书”文本框中。

    h. 具有多个空间的站点可以从“默认空间”下拉列表中选择默认空间。 这是在新用户首次登录 Periscope Data 时将它们添加到其中的空间，通过 Active Directory 单一登录进行预配。

    i. 最后，单击“保存”，并通过输入“注销”来确认 SSO 设置更改。

    ![Periscope Data 配置信息](./media/periscope-data-tutorial/configure02.png)

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

在本部分中，通过授予 Britta Simon 访问 Periscope Data 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Periscope Data”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Periscope Data”。

    ![应用程序列表中的 Periscope Data 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-periscope-data-test-user"></a>创建 Periscope Data 测试用户

若要让 Azure AD 用户登录 Periscope Data，必须将其预配到 Periscope Data 中。 在 Periscope Data 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Periscope Data。

2. 单击菜单左下角的“设置”图标，并导航到“权限”。

    ![Periscope Data 配置信息](./media/periscope-data-tutorial/configure03.png)

3. 单击“添加用户”，然后执行以下步骤：

      ![Periscope Data 配置信息](./media/periscope-data-tutorial/configure04.png)

    a. 在“名字”文本框中，输入用户的名字，如 Britta。

    b. 在“姓氏”文本框中，输入用户的名字，如 Simon。

    c. 在“电子邮件”文本框中，输入用户的电子邮件，例如 brittasimon\@contoso.com。

    d. 单击“添加”。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Periscope Data 磁贴时，应会自动登录到为其设置了 SSO 的 Periscope Data。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

