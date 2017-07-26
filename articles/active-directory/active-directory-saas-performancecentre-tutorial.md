---
title: "教程：Azure Active Directory 与 PerformanceCentre 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 PerformanceCentre 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: e86adaf4bd9b4752f2aece8207a8a423ec5590a6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>教程：Azure Active Directory 与 PerformanceCentre 的集成

本教程介绍了如何将 PerformanceCentre 与 Azure Active Directory (Azure AD) 集成。

将 PerformanceCentre 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 PerformanceCentre
- 可以让用户通过其 Azure AD 帐户自动登录到 PerformanceCentre（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 PerformanceCentre 的集成，需备齐以下项目：

- 一个 Azure AD 订阅
- 已启用 PerformanceCentre 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 PerformanceCentre
2. 配置和测试 Azure AD 单一登录

## <a name="adding-performancecentre-from-the-gallery"></a>从库中添加 PerformanceCentre
若要通过配置将 PerformanceCentre 集成到 Azure AD 中，需从库将 PerformanceCentre 添加到托管式 SaaS 应用的列表中。

**若要从库添加 PerformanceCentre，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“PerformanceCentre”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. 在结果面板中，选择“PerformanceCentre”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 PerformanceCentre 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 PerformanceCentre 用户。 换句话说，需要建立 Azure AD 用户与 PerformanceCentre 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 PerformanceCentre 中“用户名”的值来建立此关联关系。

若要使用 PerformanceCentre 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 PerformanceCentre 测试用户](#creating-a-performancecentre-test-user) - 在 PerformanceCentre 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 PerformanceCentre 应用程序中配置单一登录。

**若要通过 PerformanceCentre 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 PerformanceCentre 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. 在“PerformanceCentre 域和 URL”部分，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`http://companyname.performancecentre.com/saml/SSO`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`http://companyname.performancecentre.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [PerformanceCentre 客户端支持团队](https://www.performancecentre.com/contact-us/)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. 在“PerformanceCentre 配置”部分中，单击“配置 PerformanceCentre”打开“配置登录”窗口。 从“快速参考”部分中复制 SAML 实体 ID 和 SAML 单一登录服务 URL。

    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. 以管理员身份登录 **PerformanceCentre** 公司站点。

8. 在左侧选项卡中，单击“配置”。
   
    ![Azure AD 单一登录][10]

9. 在左侧选项卡中，单击“杂项”，然后单击“单一登录”。
   
    ![Azure AD 单一登录][11]

10. 选择“SAML”作为“协议”。
   
    ![Azure AD 单一登录][12]

11. 在记事本中打开下载的元数据文件，将其内容复制并粘贴到“标识提供者元数据”文本框中，然后单击“保存”。
   
    ![Azure AD 单一登录][13]

12. 确保“实体基 URL”和“实体 ID URL”的值正确。
    
     ![Azure AD 单一登录][14]

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-performancecentre-test-user"></a>创建 PerformanceCentre 测试用户

本部分的目的是在 PerformanceCentre 中创建名为“Britta Simon”的用户。

**若要在 PerformanceCentre 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 PerformanceCentre 公司站点。

2. 在左侧菜单中，单击“相互关联”，然后单击“创建参与者”。
   
    ![创建用户][400]

3. 在“相互关联 - 创建参与者”对话框中，执行以下步骤：
   
    ![创建用户][401]
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在相关文本框中键入 Britta Simon 的所需属性。
    
    >[!IMPORTANT]
    >Britta 在 PerformanceCentre 中的“用户名”属性必须与 Azure AD 中的“用户名”相同。
    
    b. 对于“选择角色”选项，请选择“客户端管理员”。
    
    c. 单击“保存” 。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 PerformanceCentre 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 PerformanceCentre，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“PerformanceCentre”。

    ![配置单一登录](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

单击访问面板中的“PerformanceCentre”磁贴时，用户就会自动登录到 PerformanceCentre 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png


