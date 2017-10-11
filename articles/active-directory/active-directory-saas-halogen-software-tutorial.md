---
title: "教程：Azure Active Directory 与 Halogen Software 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Halogen Software 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e09fa93038965e4880a23002bac6917ad2a077f7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>教程：Azure Active Directory 与 Halogen Software 集成

本教程介绍如何将 Halogen Software 与 Azure Active Directory (Azure AD) 集成。

将 Halogen Software 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 Halogen Software
- 可以让用户使用其 Azure AD 帐户自动登录到 Halogen Software（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Halogen Software 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 已启用 Halogen Software 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Halogen Software
2. 配置和测试 Azure AD 单一登录

## <a name="adding-halogen-software-from-the-gallery"></a>从库中添加 Halogen Software

要配置 Halogen Software 与 Azure AD 的集成，需要从库中将 Halogen Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Halogen Software，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Halogen Software”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. 在结果面板中，选择“Halogen Software”，然后单击“添加”按钮以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户使用 Halogen Software 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Halogen Software 用户。 换句话说，需要在 Azure AD 用户与 Halogen Software 中的相关用户之间建立关联关系。

通过将 Azure AD 中“用户名”的值指定为 Halogen Software 中“用户名”的值来建立此链接关系。

若要配置并测试 Halogen Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建台卤素软件测试用户](#creating-a-halogen-software-test-user)** -若要链接到用户的 Azure AD 表示台卤素软件中具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Halogen Software 应用程序中配置单一登录。

**若要配置 Halogen Software 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Halogen Software”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. 在“Halogen Software 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://global.hgncloud.com/<companyname>`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://global.halogensoftware.com/<companyname>``https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Halogen Software 客户端支持团队](https://support.halogensoftware.com/)获取这些值。 
 


4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-halogen-software-tutorial/tutorial_general_400.png)

6. 在另一个浏览器窗口中，以管理员身份登录到 **Halogen Software** 应用程序。

7. 单击“**选项**”选项卡。 
   
    ![什么是 Azure AD Connect][12]

8. 在左侧导航窗格中，单击“SAML 配置”。 
   
    ![什么是 Azure AD Connect][13]

9. 在“SAML 配置”页上，执行以下步骤： 

    ![什么是 Azure AD Connect][14]

     a. 选择 **NameID** 作为**唯一标识符**。

     b. 对于“唯一标识符映射到”，请选择“用户名”。
  
     c. 要上载已下载的元数据文件，请单击“浏览”选择该文件，并单击“上载文件”。
 
     d.单击“下一步”。 若要测试配置，请单击“运行测试”。 
    
    >[!NOTE]
    >需要等待消息“*SAML 测试已完成。请关闭此窗口*”。 然后，关闭打开的浏览器窗口。 仅在测试完成的情况下，才会启用“启用 SAML”复选框。 
     
     e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 选择“启用 SAML”。
    
     f. 单击“保存更改”。 

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入名称“BrittaSimon”。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-halogen-software-test-user"></a>创建 Halogen Software 测试用户

本部分的目的是在 Halogen Software 中创建名为“Britta Simon”的用户。

**若要在 Halogen Software 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录到 **Halogen Software** 应用程序。

2. 单击“用户中心”选项卡，并单击“创建用户”。
   
    ![什么是 Azure AD Connect][300]  

3. 在“新建用户”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][301]

    a. 在“名字”文本框中，键入用户的名字（如“Britta”）。
    
    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。 

    c. 在 Azure 门户的“用户名”文本框中，键入用户名“Britta Simon”。

    d. 在“密码”文本框中，键入 Britta 的密码。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存” 。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Halogen Software 的权限，以允许其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Halogen Software，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Halogen Software”。

    ![配置单一登录](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

当在访问面板中单击 Halogen Software 磁贴时，应当会自动登录到 Halogen Software 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png
