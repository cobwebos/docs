---
title: 教程：Azure Active Directory 与 Canvas Lms 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Canvas LMS 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: af2c997f0842da751eb93f0788a7402fc7d144ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433283"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>教程：Azure Active Directory 与 Canvas LMS 集成

本教程介绍了如何将 Canvas 与 Azure Active Directory (Azure AD) 集成。

将 Canvas 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Canvas
- 可以让用户使用其 Azure AD 帐户自动登录到 Canvas（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Canvas 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Canvas 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Canvas
1. 配置和测试 Azure AD 单一登录

## <a name="adding-canvas-from-the-gallery"></a>从库中添加 Canvas
若要配置 Canvas 与 Azure AD 的集成，需要从库中将 Canvas 添加到托管 SaaS 应用列表。

**若要从库中添加 Canvas，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在“搜索框”中，键入“Canvas”。

    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/tutorial_canvaslms_search.png)

1. 在结果面板中，选择“Canvas”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Canvas 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Canvas 用户。 换句话说，需要建立 Azure AD 用户与 Canvas 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 Canvas 中“用户名”的值来建立此关联关系。

若要配置并测试 Canvas 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Canvas 测试用户](#creating-a-canvas-test-user)** - 在 Canvas 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Canvas 应用程序中配置单一登录。

**若要配置 Canvas 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Canvas 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

1. 在“Canvas 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<tenant-name>.instructure.com`

    b. 在“标识符”文本框中，使用以下模式键入值：`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Canvas 客户端支持团队](https://community.canvaslms.com/community/help)获取这些值。 
 
1. 在“SAML 签名证书”部分中，复制证书的 THUMBPRINT 值。

    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_general_400.png)

1. 在“Canvas 配置”部分，单击“配置 Canvas”打开“配置登录”窗口。 从“快速参考”部分中复制“更改密码 URL”、“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Canvas 公司站点。

1. 转到“课程”**\>“托管帐户”\>**“Microsoft”。
   
    ![Canvas](./media/canvas-lms-tutorial/IC775990.png "Canvas")

1. 在左侧导航窗格中，选择“身份验证”，并单击“添加新 SAML 配置”。
   
    ![身份验证](./media/canvas-lms-tutorial/IC775991.png "身份验证")

1. 在“当前集成”页上，执行以下步骤：
   
    ![当前集成](./media/canvas-lms-tutorial/IC775992.png "Current Integration")

    a. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“IdP 实体 ID”文本框中。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在“更改密码链接”文本框中，粘贴从 Azure 门户复制的“更改密码 URL”值。 

    e. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。      
        
    f. 从“登录名属性”列表中，选择 **NameID**。

    g. 从“标识符格式”列表中，选择 **emailAddress**。

    h. 单击“保存身份验证设置”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/canvas-lms-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-canvas-test-user"></a>创建 Canvas 测试用户

为了使 Azure AD 用户能够登录到 Canvas，必须将其预配到 Canvas 中。

对于 Canvas，需要手动执行用户预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录“Canvas”租户。

1. 转到“课程”**\>“托管帐户”\>**“Microsoft”。
   
   ![Canvas](./media/canvas-lms-tutorial/IC775990.png "Canvas")

1. 单击“用户”。
   
   ![用户](./media/canvas-lms-tutorial/IC775995.png "用户")

1. 单击“添加新用户”。
   
   ![用户](./media/canvas-lms-tutorial/IC775996.png "用户")

1. 在“添加新用户”对话框页上，执行以下步骤：
   
   ![添加用户](./media/canvas-lms-tutorial/IC775997.png "添加用户")
   
   a. 在“全名”文本框中输入用户的姓名，例如 BrittaSimon。

   b. 在“电子邮件”文本框中，输入用户的电子邮件地址（例如 brittasimon@contoso.com）。

   c. 在“登录名”文本框中，输入用户的 Azure AD 电子邮件地址（例如 brittasimon@contoso.com）。

   d. 选择“通过电子邮件告知用户此帐户创建操作”。

   e. 单击“添加用户”。

>[!NOTE]
>可以使用 Canvas 提供的任何其他 Canvas 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Canvas 的访问权限支持她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Canvas，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Canvas”。

    ![配置单一登录](./media/canvas-lms-tutorial/tutorial_canvaslms_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Canvas 磁贴时，应该会自动登录到 Canvas 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/canvas-lms-tutorial/tutorial_general_203.png

