---
title: "教程：Azure Active Directory 与 Inkling 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Inkling 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 64c7ee45-ee8a-42f7-bf04-fd0e00833ea9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 7b0639c6515298731f88346c2e4ca82664653a2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-inkling"></a>教程：Azure Active Directory 与 Inkling 集成

本教程介绍了如何将 Inkling 与 Azure Active Directory (Azure AD) 进行集成。

将 Inkling 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Inkling
- 可以让用户使用其 Azure AD 帐户自动登录到 Inkling（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Inkling 的集成，需要以下项：

- Azure AD 订阅
- 启用的订阅上的 Inkling 单一登录


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Inkling
2. 配置和测试 Azure AD 单一登录


## <a name="adding-inkling-from-the-gallery"></a>从库中添加 Inkling
要配置 Inkling 与 Azure AD 的集成，需要从库中将 Inkling 添加到托管 SaaS 应用列表。

**若要从库中添加 Inkling，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Inkling”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_001.png)

5. 在结果窗格中，选择“Inkling”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Inkling 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Inkling 用户。 换句话说，需要建立 Azure AD 用户与 Inkling 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Inkling 中“用户名”的值来建立此链接关系。

若要通过 Inkling 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Inkling 测试用户](#creating-an-inkling-test-user)** - 在 Inkling 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中的步骤在 Azure 管理门户中启用 Azure AD 单一登录并在 Inkling 应用程序中配置单一登录。

**若要使用 Inkling 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Inkling”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_general_300.png)
    
3. 在“Inkling 域和 URL”部分中，执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_01.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://api.inkling.com/saml/v2/metadata/<user-id>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.inkling.com/saml/v2/acs/<user-id>`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际标识符和回复 URL 更新这些值。 若要获取这些值，请联系 [Inkling 支持团队](mailto:press@inkling.com)。

4. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_general_400.png)    

5. 在“创建新证书”对话框中，单击日历图标，并选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_general_500.png)

6. 在“SAML 签名证书”部分中，选择“激活新证书”，并单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_02.png)

7. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_general_600.png)

8. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_03.png) 

9. 若要为应用程序配置 SSO，请联系 [Inkling 支持团队](mailto:press@inkling.com)，并向他们提供下载的**元数据**。 


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-inkling-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-an-inkling-test-user"></a>创建 Inkling 测试用户

在本部分中，在 Inkling 中创建名为 Britta Simon 的用户。 请与 [Inkling 支持团队](mailto:press@inkling.com)协作，将用户添加到 Inkling 平台中。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Inkling 的访问权限支持她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Inkling，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Inkling”。

    ![配置单一登录](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Inkling 磁贴时，应会自动登录到 Inkling 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_203.png