---
title: 教程：Azure Active Directory 与 Igloo Software 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Igloo Software 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: cea66f48d3ec1ab99b2b1e3dc40ab559e0ca55a5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229152"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>教程：Azure Active Directory 与 Igloo Software 集成

本教程介绍如何将 Igloo Software 与 Azure Active Directory (Azure AD) 集成。

将 Igloo Software 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Igloo Software
- 可让用户使用其 Azure AD 帐户自动登录到 Igloo Software（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Igloo Software 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Igloo Software 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Igloo Software
2. 配置和测试 Azure AD 单一登录

## <a name="adding-igloo-software-from-the-gallery"></a>从库中添加 Igloo Software
若要配置 Igloo Software 与 Azure AD 的集成，需要从库中将 Igloo Software 添加到托管 SaaS 应用列表。

若要从库中添加 Igloo Software，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Igloo Software”。

    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. 在结果面板中，选择“Igloo Software”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分将基于一个名为“Britta Simon”的测试用户，配置和测试 Igloo Software 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Igloo Software 用户。 换句话说，需要建立 Azure AD 用户与 Igloo Software 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Igloo Software 中“用户名”的值来建立此链接关系。

若要配置并测试 Igloo Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Igloo Software 测试用户](#creating-an-igloo-software-test-user) - 在 Igloo Software 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Igloo Software 应用程序中配置单一登录。

若要配置 Igloo Software 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Igloo Software”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. 在“Igloo Software 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.igloocommmunities.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<company name>.igloocommmunities.com/saml.digest`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Igloo Software 客户端支持团队](https://www.igloosoftware.com/services/support)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/igloo-software-tutorial/tutorial_general_400.png)
    
6. 在“Igloo Software 配置”部分中，单击“配置 Igloo Software”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Igloo Software 公司站点。

8. 转到“控制面板”。
   
     ![控制面板](./media/igloo-software-tutorial/ic799949.png "控制面板")

9. 在“成员身份”选项卡中，单击“登录设置”。
   
    ![登录设置](./media/igloo-software-tutorial/ic783968.png "登录设置")

10. 在“SAML 配置”部分中，单击“配置 SAML 身份验证”。
   
    ![SAML 配置](./media/igloo-software-tutorial/ic783969.png "SAML 配置")
   
11. 在“常规配置”部分中，执行以下步骤：
   
    ![常规配置](./media/igloo-software-tutorial/ic783970.png "常规配置")

    a. 在“连接名称”文本框中，键入配置的自定义名称。
   
    b. 在“IdP 登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
   
    c. 在“IdP 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
    
    d. 选择“注销响应和请求 HTTP 类型”作为“POST”。
   
    e. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后粘贴到“公用证书”文本框。
    
12. 在“响应和身份验证配置”中，执行以下步骤：
    
    ![响应和身份验证配置](./media/igloo-software-tutorial/IC783971.png "响应和身份验证配置")
  
      a. 对于“标识提供者”，选择“Microsoft ADFS”。
      
      b. 对于“标识符类型”，选择“电子邮件地址”。 

      c. 在“电子邮件属性”文本框中，键入“emailaddress”。

      d. 在“名字属性”文本框中，键入“givenname”。

      e. 在“姓氏属性”文本框中，键入“surname”。

13. 请执行以下步骤以完成配置：
    
    ![登录时创建用户](./media/igloo-software-tutorial/IC783972.png "登录时创建用户") 

     a. 对于“登录时创建用户”，选择“用户登录时在站点中创建新用户”。

     b. 对于“登录设置”，选择“使用‘登录’屏幕上的 SAML 按钮”。

     c. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-an-igloo-software-test-user"></a>创建 Igloo Software 测试用户

没有操作项可用于配置预配到 Igloo Software 的用户。  

当分配的用户尝试使用访问面板登录到 Igloo Software 时，Igloo Software 会检查该用户是否存在。  如果尚无用户帐户可用，Igloo software 会自动创建该用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过授予 Britta Simon 访问 Igloo Software 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Igloo Software，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Igloo Software”。

    ![配置单一登录](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Igloo Software 磁贴时，用户应自动登录到 Igloo Software 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

