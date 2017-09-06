---
title: "教程：Azure Active Directory 与 Help Scout 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Help Scout 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教程：Azure Active Directory 与 Help Scout 集成

本教程介绍如何将 Help Scout 与 Azure Active Directory (Azure AD) 集成。

将 Help Scout 与 Azure AD 集成可带来以下优势：

- 可以在 Azure AD 中控制谁有权访问 Help Scout。
- 可以通过使用单一登录和用户的 Azure AD 帐户使用户自动登录到 Help Scout。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 的集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要设置 Azure AD 与 Help Scout 的集成，需做好以下准备：

- Azure AD 订阅
- 已启用单一登录的 Help Scout 订阅 

> [!NOTE]
> 不建议在生产环境中测试本教程中的步骤。

测试本教程中的步骤的建议：

- 除非必要，否则请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Help Scout。
2. 设置和测试 Azure AD 单一登录。

## <a name="add-help-scout-from-the-gallery"></a>从库中添加 Help Scout
若要设置 Help Scout 与 Azure AD 的集成，请在库中将 Help Scout 添加到托管 SaaS 应用列表。

从库中添加 Help Scout：

1. 在“Azure 门户”[](https://portal.azure.com)的左侧菜单中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序”，然后选择“所有应用程序”。

    ![企业应用程序页][2]
    
3. 若要添加新的应用程序，请选择“新建应用程序”。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，输入 **Help Scout**。 在搜索结果中选择“Help Scout”，并选择“添加”。

    ![结果列表中的 Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>设置和测试 Azure AD 单一登录

在本部分中，将基于名为 *Britta Simon* 的测试用户设置并测试 Help Scout 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Help Scout 用户。 必须建立 Azure AD 用户与 Help Scout 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Help Scout 中“用户名”的值来建立此关联关系。

若要配置和测试 Help Scout 的 Azure AD 单一登录，请完成以下任务：

1. [设置 Azure AD 单一登录](#set-up-azure-ad-single-sign-on)。 设置用户以使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)。 通过用户 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Help Scout 测试用户](#create-a-help-scout-test-user)。 在 Help Scout 中创建一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)。 设置 Britta Simon 以使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)。 验证配置是否正常工作。

### <a name="set-up-azure-ad-single-sign-on"></a>设置 Azure AD 单一登录

在此部分中，设置 Azure 门户中的 Azure AD 单一登录。 然后，在 Help Scout 应用程序中设置单一登录。

设置 Help Scout 的 Azure AD 单一登录：

1. 在 Azure 门户中的 Help Scout 应用程序集成页上，选择“单一登录”。
 
    ![设置单一登录链接][4]

2. 在“单一登录”页上，选择“基于 SAML 的登录”作为“模式”。
 
    ![“单一登录”对话框](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. 在“Help Scout 域和 URL”下，如果要在 IDP 发起的模式下设置应用程序，请完成以下步骤：

    1. 在“标识符”框中，输入采用以下模式的 URL：`urn:auth0:helpscout:<instancename>`

    2. 在“回复 URL”框中，输入采用以下模式的 URL：`https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Help Scout 域和 URL 单一登录信息](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. 若要在 SP 发起的模式下设置应用程序，请选中“显示高级 URL 设置”复选框，并执行以下操作：

    * 在“登录 URL”框中，输入采用以下格式的 URL：`https://secure.helpscout.net/members/login/`

    ![Help Scout 域和 URL 单一登录信息](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > 这些 URL 中的值仅用于演示。 使用实际标识符 URL 和回复 URL 更新这些值。 若要获取这些值，请联系 [Help Scout 支持团队](mailto:help@helpscout.com)。 

5. 在“SAML 签名证书”下，选择“元数据 XML”，并将元数据文件保存到计算机上。

    ![证书下载链接](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. 若要在 Help Scout 端设置单一登录，请将下载的元数据 XML 发送给 [Help Scout 支持团队](mailto:help@helpscout.com)。 Help Scout 支持团队会应用此设置，以便在两端设置正确 SAML 单一登录连接。

> [!TIP]
> 在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本！ 从“Active Directory” > “企业应用程序”添加应用后，选择“单一登录”选项卡。然后可以在页面底部的“配置”部分访问嵌入式文档。 有关详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

在 Azure AD 中创建测试用户：

1. 在 Azure 门户的左侧菜单中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![选择“用户和组”，并选择“所有用户”](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”页面顶部选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，完成以下步骤：

    1. 在“姓名”框中，输入 **BrittaSimon**。

    2. 在“用户名”框中，输入用户 Britta Simon 的电子邮件地址。

    3. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    4. 选择“创建” 。

        ![“用户”对话框](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>创建 Help Scout 测试用户

本部分的目的是在 Help Scout 中创建名为 Britta Simon 的用户。 Help Scout 支持实时 (JIT) 预配，该功能默认处于启用状态。

在此部分中，不需要完成任何操作或任务。 尝试访问 Help Scout 时，如果 Help Scout 中尚不存在用户，则系统会创建一个新用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户帐户访问 Help Scout 的权限，允许用户 Britta Simon 使用 Azure AD 单一登录。

![分配用户角色][200] 

将 Britta Simon 分配到 Help Scout：

1. 在 Azure 门户中，打开应用程序视图，并转到目录视图。 选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Help Scout”。

    ![应用程序列表中的 Help Scout 链接](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”。 然后，在“添加分配”页上，选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”页的用户列表中，选择“Britta Simon”。

6. 在“用户和组”页上，选择“选择”。

7. 在“添加分配”页上，选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择 Help Scout 磁贴时，应会自动登录到 Help Scout 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


