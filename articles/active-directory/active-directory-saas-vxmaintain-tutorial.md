---
title: 教程：Azure Active Directory 与 vxMaintain 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 vxMaintain 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c30d59a2bd903a5578caf376957a22bb270455f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>教程：Azure Active Directory 与 vxMaintain 集成

本教程介绍如何将 vxMaintain 与 Azure Active Directory (Azure AD) 集成。

此集成有以下几个重要优势。 可以：

- 可以在 Azure AD 中控制有权访问 vxMaintain 的用户。
- 可以让用户使用其 Azure AD 帐户通过单一登录 (SSO) 自动登录到 vxMaintain。
- 在一个中心位置（Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 vxMaintain 的集成，需要以下项：

- Azure AD 订阅
- 已启用 vxMaintain SSO 的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 

本教程概述的方案包括两个主要构建基块：

* 从库添加 vxMaintain
* 配置和测试 Azure AD 单一登录

## <a name="add-vxmaintain-from-the-gallery"></a>从库中添加 vxMaintain
要配置 vxMaintain 与 Azure AD 的集成，需要从库中将 vxMaintain 添加到托管 SaaS 应用列表。

若要从库添加 vxMaintain，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”按钮。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 若要添加应用程序，请在“所有应用程序”对话框中，选择“新建应用程序”。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入 **vxMaintain**。

    ![“单一登录模式”下拉列表](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. 在结果列表中，选择“vxMaintain”，然后选择“添加”。

    ![vxMaintain 链接](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，可以基于名为“Britta Simon”的测试用户通过使用 vxMaintain 配置并测试 Azure AD SSO。

为了使 SSO 正常工作，Azure AD 需要了解 Azure AD 用户对应的 vxMaintain 用户。 也就是说，必须在 Azure AD 用户和对应的 vxMaintain 用户之间建立链接关系。

若要建立链接关系，请将 vxMaintain“用户名”值分配为 Azure AD“用户名”值。

若要通过 vxMaintain 配置和测试 Azure AD SSO，请完成以下构建基块。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，我们将执行以下操作，在 Azure 门户中启用 Azure AD SSO，同时在 vxMaintain 应用程序中配置 SSO：

1. 在 Azure 门户中的“vxMaintain”应用程序集成页上，选择“单一登录”。

    ![“单一登录”命令][4]

2. 若要启用 SSO，请在“单一登录模式”下拉列表中选择“基于 SAML 的登录”。
 
    ![“基于 SAML 的登录”命令](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. 在“vxMaintain 域和 URL”下，执行以下操作：

    ![“vxMaintain 域和 URL”部分](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. 在“标识符”框中，键入包含 `https://<company name>.verisae.com` 语句的 URL

    b. 在“回复 URL”框中，键入具有以下语法的 URL：`https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > 上面的值不是实际值。 使用实际标识符和回复 URL 更新它们。 若要获取这些值，请联系 [vxMaintain 支持团队](http://www.verisae.com/contact-us)。
 
4. 在“SAML 签名证书”下，选择“元数据 XML”，并将元数据文件保存到计算机上。

    ![“SAML 签名证书”部分](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. 选择“保存”。

    ![点击“保存”按钮](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. 若要配置“vxMaintain”SSO，请将下载的“元数据 XML”文件发送到 [vxMaintain 支持团队](http://www.verisae.com/contact-us)。

> [!TIP]
> 设置应用时，可以在 [Azure 门户](https://portal.azure.com)中阅读前述教程的简洁版本。 从“Active Directory” > “企业应用程序”部分添加此应用后，选择“单一登录”选项卡，即可通过“配置”部分访问嵌入式文档。 
>
>若要了解嵌入式文档功能的详细信息，请参阅[管理适用于企业应用的单一登录](https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

![Azure AD 测试用户][100]

1. 在“Azure 门户”的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组” > “所有用户”。
    
    ![“所有用户”链接](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    “所有用户”对话框随即打开。 

3. 若要打开“用户”对话框，请选择“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中执行以下操作：
 
    ![“用户”对话框](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入测试用户 Britta Simon 的电子邮件地址。

    c. 选择“显示密码”复选框，然后记下“密码”框中生成的值。

    d. 选择**创建**。
 
### <a name="create-a-vxmaintain-test-user"></a>创建 vxMaintain 测试用户

在本部分中，会在 vxMaintain 中创建测试用户 Britta Simon。 与 [vxMaintain 支持团队](http://www.verisae.com/contact-us)一起将用户添加到 vxMaintain 平台。 在使用 SSO 前创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予测试用户 Britta Simon 访问 vxMaintain 的权限，允许其使用 Azure SSO。 为此，请执行以下操作：

![“显示名称”列表中的测试用户][200] 

1. 在 Azure 门户的“应用程序”视图中，转到“目录”视图 >“企业应用程序” > “所有应用程序”。

    ![“所有应用程序”链接][201] 

2. 在“应用程序”列表中，选择“vxMaintain”。

    ![vxMaintain 链接](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202] 

4. 选择“添加”，然后在“添加分配”窗格中选择“用户和组”。

    ![“用户和组”链接][203]

5. 在“用户和组”对话框中的“用户”列表中，选择“Britta Simon”，然后选择“选择”按钮。

7. 在“添加分配”对话框中选择“分配”。
    
### <a name="test-your-azure-ad-single-sign-on"></a>测试 Azure AD 单一登录

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

选择“访问面板”中的“vxMaintain”磁贴时，会自动登录到 vxMaintain 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

* [有关将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

