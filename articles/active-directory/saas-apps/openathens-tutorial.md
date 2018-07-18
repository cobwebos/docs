---
title: 教程：Azure Active Directory 与 OpenAthens 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 OpenAthens 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: d29904b58107e84744fb9469956e4b321bab4e88
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230465"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>教程：Azure Active Directory 与 OpenAthens 集成

在本教程中，了解如何将 OpenAthens 与 Azure Active Directory (Azure AD) 集成。

将 OpenAthens 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 OpenAthens。
- 可以让用户使用其 Azure AD 帐户自动登录到 OpenAthens（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 OpenAthens 的集成，需要以下项：

- Azure AD 订阅
- 已启用 OpenAthens 单一登录的订阅

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 OpenAthens
2. 配置和测试 Azure AD 单一登录

## <a name="adding-openathens-from-the-gallery"></a>从库中添加 OpenAthens
若要配置 OpenAthens 与 Azure AD 的集成，需要从库中将 OpenAthens 添加到托管 SaaS 应用列表。

**从库中添加 OpenAthens**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 浏览到“企业应用程序”，并转到“所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“OpenAthens”，在结果面板中选择“OpenAthens”，并选择“添加”按钮。

    ![结果列表中的 OpenAthens](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 OpenAthens 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 OpenAthens 用户。 换句话说，需要建立 Azure AD 用户与 OpenAthens 中相关用户的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 OpenAthens 中“用户名”的值来建立此链接关系。

若要配置并测试 OpenAthens 的 Azure AD 单一登录，需要完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，以使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，目的是通过 Britta Simon 测试 Azure AD 单一登录。
3. [创建 OpenAthens 测试用户](#create-a-openathens-test-user)：在 OpenAthens 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 OpenAthens 应用程序中配置单一登录。

**配置 OpenAthens 的 Azure AD 单一登录**

1. 在 Azure 门户中的 **OpenAthens** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 若要启用单一登录，请在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”。
 
    ![“单一登录”对话框](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

3. 在“OpenAthens 域和 URL”部分的“标识符”文本框中输入值 `https://login.openathens.net/saml/2/metadata-sp`。

    ![OpenAthens 域和 URL 单一登录信息](./media/openathens-tutorial/tutorial_openathens_url.png)

4. 在“SAML 签名证书”部分中，选择“元数据 XML”，并在计算机上保存元数据文件。

    ![AMSL 签名证书下载链接](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

5. 选择“保存”按钮。

    ![单一登录“保存”按钮](./media/openathens-tutorial/tutorial_general_400.png)

6. 在另一个 Web 浏览器窗口中，以管理员身份登录 OpenAthens 公司站点。

7. 在“管理”选项卡下的列表中选择“连接”。 

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application1.png)

8. 选择“SAML 1.1/2.0”，再选择“配置”按钮。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
9. 若要添加配置，请选择“浏览”按钮上传已从 Azure 门户下载的元数据 .xml 文件，再选择“添加”。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application3.png)

10. 在“详细信息”选项卡下执行以下步骤。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. 在“显示名称映射”中，选择“使用属性”。

    b. 在“显示名称属性”文本框中，输入值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。
    
    c. 在“唯一用户映射”中，选择“使用属性”。

    d. 在“唯一用户属性”文本框中，输入值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    e. 在“状态”中，选中所有三个复选框。

    f. 在“创建本地帐户”中，选择“自动”。

    g. 选择“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 有关嵌入式文档功能的详细信息，请参阅 [Azure AD 嵌入式文档](https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为“Britta Simon”的测试用户。

   ![创建 Azure AD 测试用户][100]

**在 Azure AD 中创建测试用户**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/openathens-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/openathens-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/openathens-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/openathens-tutorial/create_aaduser_04.png)

    a. 在“名称”文本框中，键入“BrittaSimon”。

    b. 在“用户名”文本框中，键入 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”文本框中显示的值。

    d. 选择**创建**。
  
### <a name="create-an-openathens-test-user"></a>创建 OpenAthens 测试用户

OpenAthens 支持实时预配，并在成功身份验证后自动创建用户。 在本部分中无需执行任何操作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 OpenAthens 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**将 Britta Simon 分配到 OpenAthens**

1. 在 Azure 门户中，打开应用程序视图，浏览到目录视图，转到“企业应用程序”，并选择“所有应用程序”。

    ![分配用户][201] 

2. 在“应用程序”列表中，选择“OpenAthens”。

    ![应用程序列表中的 OpenAthens 链接](./media/openathens-tutorial/tutorial_openathens_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮。 然后，在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”列表中，选择“Britta Simon”。

6. 在“用户和组”列表中，选择“选择”按钮。

7. 在“添加分配”窗格中选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择访问面板中的“OpenAthens”磁贴时，应会自动登录到 OpenAthens 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* 有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表，请参阅[有关配合 Azure AD 使用 SaaS 应用集成的教程](tutorial-list.md)。
* 有关 Azure Active Directory 的应用程序访问和单一登录的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)。

<!--Image references-->

[1]: ./media/openathens-tutorial/tutorial_general_01.png
[2]: ./media/openathens-tutorial/tutorial_general_02.png
[3]: ./media/openathens-tutorial/tutorial_general_03.png
[4]: ./media/openathens-tutorial/tutorial_general_04.png

[100]: ./media/openathens-tutorial/tutorial_general_100.png

[200]: ./media/openathens-tutorial/tutorial_general_200.png
[201]: ./media/openathens-tutorial/tutorial_general_201.png
[202]: ./media/openathens-tutorial/tutorial_general_202.png
[203]: ./media/openathens-tutorial/tutorial_general_203.png

