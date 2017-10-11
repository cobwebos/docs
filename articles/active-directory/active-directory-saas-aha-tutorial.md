---
title: "教程：Azure Active Directory 与 Aha! 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Aha! 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 7723864b2e1ab2d5b69d86f0fa18416b9d3f9aa3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>教程：Azure Active Directory 与 Aha! 集成

在本教程中，你将学习如何将 Aha!  与 Azure Active Directory (Azure AD) 集成。

将 Aha!  与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 Aha!
- 可让用户通过其 Azure AD 帐户自动登录到 Aha! （单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Aha! 的集成，需要以下项目：

- 一个 Azure AD 订阅
- Aha! 已启用单一登录的订阅上

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中 添加 Aha!
2. 配置和测试 Azure AD 单一登录

## <a name="adding-aha-from-the-gallery"></a>从库中 添加 Aha!
若要配置 Aha!  与 Azure AD 的集成，你需要将 Aha! 从库中 添加到托管式 SaaS 应用的列表中。

**若要从库中添加 Aha!，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在“搜索框”中键入“Aha!”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/tutorial_aha_search.png)

5. 在结果面板中，选择“Aha!”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Aha!  配置和测试 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解与 Azure AD 中的用户相对应的 Aha!  用户。 换句话说，需要建立 Azure AD 用户与 Aha! 用户 之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Aha! 中“用户名”的值来建立此链接关系。

若要配置和测试 Aha! 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Aha! 测试用户](#creating-an-aha-test-user) - 在 Aha! 中创建 Britta Simon 的对应用户， 并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Aha! 应用程序中 配置单一登录。

**若要配置 Azure AD 单一登录与 Aha ！，执行以下步骤：**

1. 在 Azure 门户的 Aha!  应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-aha-tutorial/tutorial_aha_samlbase.png)

3. 在“Aha! 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-aha-tutorial/tutorial_aha_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.aha.io/session/new`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.aha.io`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [Aha! 客户端支持团队](https://www.aha.io/company/contact)。 
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-aha-tutorial/tutorial_aha_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-aha-tutorial/tutorial_general_400.png)

6. 在其他 Web 浏览器窗口中，作为管理员登录 Aha!  公司站点。

7. 在顶部菜单中，单击“设置”。

    ![设置](./media/active-directory-saas-aha-tutorial/IC798950.png "设置")

8. 单击“帐户”。
   
    ![配置文件](./media/active-directory-saas-aha-tutorial/IC798951.png "配置文件")

9. 单击“安全性和单一登录”。
   
    ![安全性和单一登录](./media/active-directory-saas-aha-tutorial/IC798952.png "安全性和单一登录")

10. 在“单一登录”部分中，对于“标识提供者”，选择“SAML2.0”。
   
    ![安全性和单一登录](./media/active-directory-saas-aha-tutorial/IC798953.png "安全性和单一登录")

11. 在“单一登录”配置页上，执行以下步骤：
    
    ![单一登录](./media/active-directory-saas-aha-tutorial/IC798954.png "单一登录")
    
       a. 在“名称”文本框中，键入配置名称。

       b. 对于“配置使用”，请选择“元数据文件”。
   
       c. 若要上传下载的元数据文件，请单击“浏览”。
   
       d. 单击“更新”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-aha-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-aha-test-user"></a>创建 Aha!  测试用户

为使 Azure AD 用户能够登录 Aha!，必须对其进行预配才能使其登录 Aha!。  

就 Aha! 来说，预配是自动化任务。 不存在任何操作项。

如有必要，在第一次尝试单一登录时会自动创建用户。

>[!NOTE]
>可以使用其他任何 Aha! 用户帐户创建工具或 Aha! 提供的 API 预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Aha! 的访问权限支持使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta 人 Simon 分配到 Aha ！，执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Aha!”。

    ![配置单一登录](./media/active-directory-saas-aha-tutorial/tutorial_aha_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-aha-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aha-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aha-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aha-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aha-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aha-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aha-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aha-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aha-tutorial/tutorial_general_203.png

