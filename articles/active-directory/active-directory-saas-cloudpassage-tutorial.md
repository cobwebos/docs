---
title: "教程：Azure Active Directory 与 CloudPassage 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 CloudPassage 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01676cbaf2b677b43a223cf33699710c77da41fa


---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>教程：Azure Active Directory 与 CloudPassage 的集成
本教程的目的是展示如何将 CloudPassage 与 Azure Active Directory (Azure AD) 进行集成。  
将 CloudPassage 与 Azure AD 集成可提供以下优势： 

* 可以在 Azure AD 中控制谁有权访问 CloudPassage 
* 可以让用户使用其 Azure AD 帐户自动登录到 CloudPassage（单一登录）
* 可以在一个中心位置（即 Azure Active Directory）管理帐户 

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 CloudPassage 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 CloudPassage 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 测试环境，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取为期一月的免费 Azure 试用版订阅。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 CloudPassage 
2. 配置和测试 Azure AD 单一登录

## <a name="adding-cloudpassage-from-the-gallery"></a>从库中添加 CloudPassage
若要配置 CloudPassage 与 Azure AD 的集成，需要从库中将 CloudPassage 添加到托管 SaaS 应用列表。

### <a name="to-add-cloudpassage-from-the-gallery-perform-the-following-steps"></a>若要从库中添加 CloudPassage，请执行以下步骤：
1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“CloudPassage”。
   
    ![应用程序][5]
7. 在结果窗格中，选择“CloudPassage”，然后单击“完成”以添加该应用程序。
   
    ![应用程序][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是基于名为“Britta Simon”的测试用户展示如何配置并测试 CloudPassage 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 CloudPassage 用户。 换句话说，需要在 Azure AD 用户与 CloudPassage 中的相关用户之间建立链接关系。  
可以通过将 Azure AD 中“用户名”的值分配为 CloudPassage 中“用户名”的值来建立此链接关系。

若要配置并测试 CloudPassage 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 CloudPassage 测试用户](#creating-a-halogen-software-test-user)** - 在 CloudPassage 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure AD 经典门户中启用 Azure AD 单一登录并在 CloudPassage 应用程序中配置单一登录。  
CloudPassage 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示一个示例。

![配置单一登录][21]

**若要配置 CloudPassage 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **CloudPassage** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][7]
2. 在“你希望用户如何登录到 CloudPassage”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录][8]
3. 在“配置应用设置”对话框页上，执行以下步骤： 
   
    ![配置应用设置][9]
   
    a. 在“登录 URL”文本框中，键入用户用来登录 CloudPassage 应用的 URL（例如：*https://portal.cloudpassage.com/saml/init/accountid*）。 
   
    b. 在“回复 URL”文本框中，键入 AssertionConsumerService URL（例如：*https://portal.cloudpassage.com/saml/consume/accountid*）。 可以通过在 CloudPassage 门户的“单一登录设置”部分中单击“SSO 设置文档”来获取此属性的值。  
    ![配置单一登录][10]
   
    C. 单击“下一步”。
4. 在“在 CloudPassage 处配置单一登录”页上，单击“下载证书”，然后将证书文件保存在计算机本地。 
   
    ![配置单一登录][11]
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 CloudPassage 公司站点。
6. 在顶部菜单中，单击“设置”，然后单击“网站管理”。 
   
    ![配置单一登录][12]
7. 单击“身份验证设置”选项卡。 
   
    ![配置单一登录][13]
8. 在“单一登录设置”部分中，执行以下步骤： 
   
    ![配置单一登录][14]

    a. 在 Azure 经典门户中，在“在 CloudPassage 处配置单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“SAML 颁发者 URL”文本框中。

    b. 在 Azure 经典门户中，在“在 CloudPassage 处配置单一登录”对话框页上，复制“服务提供商(SP)启动的终结点”值，然后将其粘贴到“SAML 终结点 URL”文本框中。

    c. 在 Azure 经典门户中，在“在 CloudPassage 处配置单一登录”对话框页上，复制“注销 URL”值，然后将其粘贴到“注销登陆页面”文本框中。

    d. 基于下载的证书创建一个 **base-64** 编码的文件。 

    >[AZURE.TIP] 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“x 509 证书”文本框中。

    f. 单击“保存”。


1. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![配置单一登录][15]
2. 在“单一登录确认”页上，单击“完成”。 
   
   ![配置单一登录][16]
3. 在顶部菜单中，单击“属性”以打开“SAML 令牌属性”对话框。 
   
   ![配置单一登录][17]
4. 若要添加必需的用户属性，请对下表中的每一行执行以下步骤： 
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | firstname |user.givenname |
   | lastname |user.surname |
   | email |user.mail |

    a. 单击“添加用户属性”。 

    ![配置单一登录][18]

    b. 在“属性名称”文本框中，键入为该行显示的属性名称，对于“属性值”，选择为该行显示的属性值。 

    ![配置单一登录][19]

    c. 单击“完成”。


1. 在底部工具栏中，单击“应用更改”。 
   
   ![配置单一登录][20]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
   
   a. 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d. 在“角色”列表中，选择“用户”。
   
   e. 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-cloudpassage-test-user"></a>创建 CloudPassage 测试用户
本部分的目的是在 CloudPassage 中创建名为 Britta Simon 的用户。

#### <a name="to-create-a-user-called-britta-simon-in-cloudpassage-perform-the-following-steps"></a>若要在 CloudPassage 中创建名为 Britta Simon 的用户，请执行以下步骤：
1. 以管理员身份登录到你的 **CloudPassage** 公司站点。 
2. 在顶部工具栏中，单击“设置”，然后单击“网站管理”。 
   
   ![创建 CloudPassage 测试用户][22] 
3. 单击“用户”选项卡，然后单击“添加新用户”。 
   
   ![创建 CloudPassage 测试用户][23]
4. 在“添加新用户”部分中，执行以下步骤： 
   
   ![创建 CloudPassage 测试用户][24]
   
   a. 在“名字”文本框中，键入“Britta”。
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“用户名”文本框、“电子邮件”文本框和“重新键入电子邮件”文本框中，键入 Britta 在 Azure AD 中的用户名。
   
   d. 对于“访问类型”，选择“启用 Halo 门户访问”。
   
   e. 单击“添加”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 CloudPassage 的访问权限使她能够使用 Azure 单一登录。

![分配用户][30]

**若要将 Britta Simon 分配到 CloudPassage，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][26]
2. 在应用程序列表中，选择“CloudPassage”。
   
    ![分配用户][27]
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][25]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][29]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。  
当在访问面板中单击 CloudPassage 磁贴时，应当会自动登录到 CloudPassage 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png
























<!--HONumber=Nov16_HO3-->


