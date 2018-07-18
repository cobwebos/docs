---
title: 教程：Azure Active Directory 与 BetterWorks 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BetterWorks 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 35293869b066c593c18c01f08fa7f13c48e5f84a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212200"
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>教程：Azure Active Directory 与 BetterWorks 集成

在本教程中，了解如何将 BetterWorks 与 Azure Active Directory (Azure AD) 进行集成。

将 BetterWorks 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 BetterWorks
- 可使用户通过其 Azure AD 帐户自动登录 BetterWorks（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 BetterWorks 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 BetterWorks 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 BetterWorks
2. 配置和测试 Azure AD 单一登录

## <a name="adding-betterworks-from-the-gallery"></a>从库中添加 BetterWorks
要配置 BetterWorks 与 Azure AD 的集成，需要将库中的 BetterWorks 添加到托管的 SaaS 应用列表。

**若要从库中添加 BetterWorks，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“BetterWorks”。

    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/tutorial_betterworks_search.png)

5. 在“结果”窗格中，选择“BetterWorks”，再单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“BetterWorks”的测试用户的指示配置和测试 BetterWorks 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 BetterWorks 用户。 换句话说，需要建立 Azure AD 用户与 BetterWorks 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 BetterWorks 中“用户名”的值来建立此链接关系。

若要配置和测试 BetterWorks 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 BetterWorks 测试用户](#creating-a-betterworks-test-user)** - 在 BetterWorks 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 BetterWorks 应用程序中配置单一登录。

**若要配置 BetterWorks 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“BetterWorks”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_samlbase.png)

3. 在“Absorb LMS 域和 URL”部分中，如果要在“IDP 启动的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://app.betterworks.com/saml2/metadata/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://app.betterworks.com/saml2/acs/`

4. 在“BetterWorks 域和 URL”部分中，如果要在 SP 启动的模式下配置应用程序，请执行以下步骤：
    
    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_url1.png)

    a. 单击“显示高级 URL 设置”。

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.betterworks.com`

    > [!NOTE] 
    > 这些不是实际值。 请使用回复 URL、标识符和实际登录 URL 更新这些值。 若要获取这些值，请与 [BetterWorks 支持团队](mailto:support@betterworks.com)联系。
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_certificate.png)  

5. BetterWorks 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_attribute.png)

6. 在“SAML 令牌属性”对话框中，对于下表中显示的每个行，执行以下步骤：
 
   | 属性名称 | 属性值 |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/betterworks-tutorial/tutorial_officespace_04.png)

    ![配置单一登录](./media/betterworks-tutorial/tutorial_officespace_05.png)

   b. 在“名称”文本框中，键入为该行显示的属性名称。 

   c. 在“值”列表中，选择为该行显示的属性值。
    
   d. 单击“确定” 。

7. 单击“保存”按钮。

    ![配置单一登录](./media/betterworks-tutorial/tutorial_general_400.png)

8. 若要在“BetterWorks”端配置单一登录，需要将下载的元数据 XML 发送给 [BetterWorks 支持团队](mailto:support@betterworks.com)。


> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/betterworks-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-betterworks-test-user"></a>创建 BetterWorks 测试用户

在本部分中，在 BetterWorks 中创建名为 Britta Simon 的用户。 请与 [BetterWorks 支持团队](mailto:support@betterworks.com)协作，以将用户添加到 BetterWorks 平台。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 BetterWorks 的权限，以支持其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 BetterWorks，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“BetterWorks”。

    ![配置单一登录](./media/betterworks-tutorial/tutorial_betterworks_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击“BetterWorks”磁贴时，应该会自动登录“BetterWorks”应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/betterworks-tutorial/tutorial_general_01.png
[2]: ./media/betterworks-tutorial/tutorial_general_02.png
[3]: ./media/betterworks-tutorial/tutorial_general_03.png
[4]: ./media/betterworks-tutorial/tutorial_general_04.png

[100]: ./media/betterworks-tutorial/tutorial_general_100.png

[200]: ./media/betterworks-tutorial/tutorial_general_200.png
[201]: ./media/betterworks-tutorial/tutorial_general_201.png
[202]: ./media/betterworks-tutorial/tutorial_general_202.png
[203]: ./media/betterworks-tutorial/tutorial_general_203.png

