---
title: "教程：Azure Active Directory 与 15Five 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 15Five 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: ea36774747a0fcfa4ace1aefb2d46dba815d92c4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>教程：Azure Active Directory 与 15Five 的集成

在本教程中，了解如何将 15Five 与 Azure Active Directory (Azure AD) 集成。

将 15Five 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 15Five
- 可以让用户使用其 Azure AD 帐户自动登录到 15Five（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 15Five 的集成，需要以下项目：

- 一个 Azure AD 订阅
- 已启用 15Five 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 15Five
2. 配置和测试 Azure AD 单一登录

## <a name="adding-15five-from-the-gallery"></a>从库中添加 15Five
若要配置 15Five 与 Azure AD 的集成，需要从库中将 15Five 添加到托管的 SaaS 应用列表。

若要从库中添加 15Five，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在“搜索框”中，键入“15Five”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/tutorial_15five_search.png)

5. 在结果面板中，选择“15Five”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/tutorial_15five_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 15Five 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 15Five 用户。 换句话说，需要建立 Azure AD 用户与 15Five 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 15Five 中“用户名”的值，建立此链接关系。

若要配置和测试 15Five 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 15Five 测试用户](#creating-a-15five-test-user) - 在 15Five 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 15Five 应用程序中配置单一登录。

若要配置 15Five 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“15Five”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-15five-tutorial/tutorial_15five_samlbase.png)

3. 在“15Five 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-15five-tutorial/tutorial_15five_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.15five.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.15five.com/saml2/metadata/`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [15Five 客户端支持团队](https://www.15five.com/contact/)，获取这些值。 
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-15five-tutorial/tutorial_15five_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-15five-tutorial/tutorial_general_400.png)

6. 若要在“15Five”端配置单一登录，需要将下载的元数据 XML 发送给 [15Five 支持团队](https://www.15five.com/contact/)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-15five-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-15five-test-user"></a>创建 15Five 测试用户

要使 Azure AD 用户能够登录到 15Five，必须将其预配到 15Five 中。 对于 15Five，需要手动完成预配操作。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录到 **15Five** 公司站点。

2. 转到“管理公司”。
   
    ![管理公司](./media/active-directory-saas-15five-tutorial/IC784675.png "管理公司")

3. 转到“人员\>添加人员”。
   
    ![人员](./media/active-directory-saas-15five-tutorial/IC784676.png "人员")

4. 在“添加新人员”部分中，执行以下步骤：
   
    ![添加新人员](./media/active-directory-saas-15five-tutorial/IC784677.png "添加新人员")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在相关文本框中键入要预配的有效 Azure Active Directory 帐户的“名字”、“姓氏”、“职位”、“电子邮件地址”。

    b. 单击“Done”（完成） 。
   
    > [!NOTE]
    > Azure AD 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
   
### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予访问 15Five 的权限，允许 Britta Simon 使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 15Five，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“15Five”。

    ![配置单一登录](./media/active-directory-saas-15five-tutorial/tutorial_15five_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 15Five 磁贴时，应显示 15Five 应用程序的登录页。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-15five-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-15five-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-15five-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-15five-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-15five-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-15five-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-15five-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-15five-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-15five-tutorial/tutorial_general_203.png


