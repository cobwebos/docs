---
title: 教程：Azure Active Directory 与 Tableau Server 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Tableau Server 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 84ea1d999a26ce0ce1d548da92549c6a718d5978
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850357"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>教程：Azure Active Directory 与 Tableau Server 集成

本教程介绍如何将 Tableau Server 与 Azure Active Directory (Azure AD) 集成。

将 Tableau Server 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Tableau Server。
- 可以让用户使用其 Azure AD 帐户自动登录到 Tableau Server（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Tableau Server 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Tableau Server 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Tableau Server
2. 配置和测试 Azure AD 单一登录

## <a name="adding-tableau-server-from-the-gallery"></a>从库中添加 Tableau Server

要配置 Tableau Server 与 Azure AD 的集成，需要从库中将 Tableau Server 添加到托管 SaaS 应用列表。

**若要从库中添加 Tableau Server，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Tableau Server”，在结果面板中选择“Tableau Server”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Tableau Server](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Tableau Server 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Tableau Server 用户。 换句话说，需要在 Azure AD 用户与 Tableau Server 中相关用户之间建立链接关系。

若要配置和测试 Tableau Server 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Tableau Server 测试用户](#creating-a-tableau-server-test-user)** - 在 Tableau Server 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录，并在 Tableau Server 应用程序中配置单一登录。

**若要配置 Tableau Server 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Tableau Server”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial-general-301.png)

3. Tableau Server 应用程序需要一个应当如下定义的自定义声明 **username**。 这用作用户标识符，而不是唯一的用户标识符声明。 可以在应用程序集成页的“用户属性和声明”部分管理这些属性的值。 单击“编辑”按钮以打开“用户属性和声明”对话框。

    ![图像](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. 在“用户属性和声明”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ---------------| --------------- |    
    | username | user.userprincipalname |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![图像](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 输入“命名空间”值。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“ **保存**”。

5. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

6. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://azure.<domain name>.link`
    
    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://azure.<domain name>.link`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![图像](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > 上面的值不是实际值。 请使用来自 Tableau Server 配置页面的实际 URL 和标识符更新这些值，本教程下文中介绍了此页面。

7. 在“SAML 签名证书”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将证书文件保存在计算机上。

    ![证书下载链接](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png) 

8. 若要为应用程序配置 SSO，需要以管理员身份登录 Tableau Server 租户。

9. 在“Tableau Server 配置”页面上，执行以下步骤：
   
    ![配置单一登录](./media/tableauserver-tutorial/tutorial-tableauserver-001.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 Tableau Server 配置中，单击“SAML”选项卡。 
  
    b. 选中“使用 SAML 进行单一登录”复选框。
   
    c. Tableau Server 返回 URL（Tableau Server 用户将要访问的 URL），例如 http://tableau_server。 但不建议使用 http://localhost。 不支持使用带尾部反斜杠的 URL（例如， http://tableau_server/)。 在“Tableau Server 域和 URL”部分中，复制“Tableau Server 返回 URL”并将其粘贴到 Azure AD“登录 URL”文本框中。
   
    d. SAML 实体 ID - 此实体 ID 唯一标识安装到 IdP 的 Tableau Server。 可以在此处再次输入 Tableau Server URL（如果需要），但它不必须是 Tableau Server URL。 在“Tableau Server 域和 URL”部分中，复制“SAML 实体 ID”并将其粘贴到 Azure AD“标识符”文本框中。
     
    e. 单击“导出元数据文件”，并在文本编辑器应用程序中打开该文件。 找到包含 Http Post 和索引 0 的断言使用者服务 URL 并复制该 URL。 现在将其粘贴到“Tableau Server 域和 URL”部分中的 Azure AD“答复 URL”文本框中。
   
    f. 找到从 Azure 门户下载的联合元数据文件，然后在“SAML Idp 元数据文件”中上传它。
   
    g. 在“Tableau Server 配置”页中单击“确定”按钮。
   
    >[!NOTE] 
    >客户必须上传 Tableau Server SAML SSO 配置中的任何证书，SSO 流程中会将其忽略。
    >如果需要帮助在 Tableau Server 上配置 SAML，请参阅此文：[配置 SAML](https://onlinehelp.tableau.com/current/server/en-us/config_saml.htm)。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create-aaduser-01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create-aaduser-02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
  
### <a name="creating-a-tableau-server-test-user"></a>创建 Tableau Server 测试用户

本部分的目的是在 Tableau Server 中创建名为“Britta Simon”的用户。 需要在 Tableau Server 中预配所有用户。 

用户的用户名应与在 **username** 的 Azure AD 自定义属性中配置的值匹配。 使用正确的映射，此集成应可实现[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)。

>[!NOTE]
>如果需要手动创建用户，需要联系组织中的 Tableau Server 管理员。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Tableau Server 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Tableau Server”。

    ![配置单一登录](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Tableau Server”磁贴时，用户应自动登录到 Tableau Server 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
