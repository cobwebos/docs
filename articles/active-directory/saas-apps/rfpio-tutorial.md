---
title: 教程：Azure Active Directory 与 RFPIO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 RFPIO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 04ba94e3263af03279b74b4832b8291ad6414274
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426554"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>教程：Azure Active Directory 与 RFPIO 集成

本教程介绍如何将 RFPIO 与 Azure Active Directory (Azure AD) 集成。

将 RFPIO 与 Azure AD 集成具有以下优势：

- 可控制谁在 Azure AD 中有权访问 RFPIO。
- 可使用户通过其 Azure AD 帐户自动登录 RFPIO（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 RFPIO 的集成，需要以下项目：

- 一个 Azure AD 订阅。
- 启用了 RFPIO 单一登录的订阅。

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 RFPIO。
1. 配置和测试 Azure AD 单一登录。

## <a name="add-rfpio-from-the-gallery"></a>从库中添加 RFPIO
若要配置 RFPIO 与 Azure AD 的集成，需要从库中将 RFPIO 添加到托管 SaaS 应用列表。

### <a name="to-add-rfpio-from-the-gallery"></a>从库中添加 RFPIO

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 选择“企业应用程序”，然后选择“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在搜索框中，键入“RFPIO”。

    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. 在结果窗格中，选择“RFPIO”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 RFPIO 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道 RFPIO 用户与 Azure AD 用户之间的对应关系。 换句话说，需要建立 Azure AD 用户与 RFPIO 中相关用户之间的链接关系。

在 RFPIO 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 RFPIO 的 Azure AD 单一登录，需要完成以下构建基块：

1. [配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on) - 使用户能够使用此功能。
1. [创建 Azure AD 测试用户](#creating-an-azure-ad-test-user) - 通过 Britta Simon 测试 Azure AD 单一登录。
1. [创建 RFPIO 测试用户](#creating-a-rfpio-test-user) - 在 RFPIO 中创建对应的 Britta Simon 用户，该用户链接到其 Azure AD 用户。
1. [分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user) - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. [测试单一登录](#testing-single-sign-on) - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分将在 Azure 门户中启用 Azure AD 单一登录并在 RFPIO 应用程序中配置单一登录。

若要配置 RFPIO 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 RFPIO 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. 在“RFPIO 域和 URL”部分中，如果要在“IDP 启动的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://www.rfpio.com`

    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. 选中“显示高级 URL 设置”。

    c. 在“中继状态”文本框中输入的字符串值。 请联系 [RFPIO 支持团队](https://www.rfpio.com/contact/)获取此值。 

1. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序： 

    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    在“登录 URL”文本框中，键入 URL： `https://www.app.rfpio.com`

1. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/rfpio-tutorial/tutorial_general_400.png)

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 RFPIO 网站。

1. 单击左下角的下拉列表。

    ![配置单一登录](./media/rfpio-tutorial/app1.png)

1. 单击“组织设置”。 

    ![配置单一登录](./media/rfpio-tutorial/app2.png)

1. 单击“功能和集成”。

    ![配置单一登录](./media/rfpio-tutorial/app4.png)

1. 在 SAML SSO 配置中，单击“编辑”。

    ![配置单一登录](./media/rfpio-tutorial/app3.png)

1. 在本部分中，执行以下操作：

    ![配置单一登录](./media/rfpio-tutorial/app5.png)
    
    a. 复制下载的元数据 XML 文件的内容，然后将其粘贴到“标识配置”字段。

    > [!NOTE]
    >要复制下载的元数据 XML的内容，请使用“Notepad++”或“XML 编辑器”。 

    b. 单击“验证”。

    c. 单击“验证”后，将“SAML(启用)”改为“开启”。

    d. 单击“提交”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="create-a-rfpio-test-user"></a>创建 RFPIO 测试用户

为了使 Azure AD 用户能够登录到 RFPIO，必须将其预配到 RFPIO 中。  
对于 RFPIO，需要手动执行预配任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 RFPIO 公司站点。

1. 单击左下角的下拉列表。

    ![配置单一登录](./media/rfpio-tutorial/app1.png)

1. 单击“组织设置”。 

    ![配置单一登录](./media/rfpio-tutorial/app2.png)

1. 单击“团队成员”。

    ![配置单一登录](./media/rfpio-tutorial/app6.png)

1. 单击“添加成员”。

    ![配置单一登录](./media/rfpio-tutorial/app7.png)

1. 在“添加新成员”部分。 执行以下操作：

    ![配置单一登录](./media/rfpio-tutorial/app8.png)

    a. 在“每行输入一个电子邮件”字段输入电子邮件地址。

    b. 根据需要选择角色。

    c. 单击“添加成员”。
        
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 RFPIO 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 RFPIO，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“RFPIO”。

    ![配置单一登录](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 RFPIO 磁贴时，应会自动登录到 RFPIO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

