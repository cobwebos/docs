---
title: 教程：Azure Active Directory 与 Fieldglass 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Fieldglass 间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 446eac2bf6eed10f919d6c64de1d7b9dbba1f35d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420340"
---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>教程：Azure Active Directory 与 Fieldglass 的集成

本教程介绍如何将 Fieldglass 与 Azure Active Directory (Azure AD) 集成。

将 Fieldglass 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Fieldglass
- 可以让用户使用其 Azure AD 帐户自动登录到 Fieldglass（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Fieldglass 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Fieldglass 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Fieldglass
1. 配置和测试 Azure AD 单一登录

## <a name="adding-fieldglass-from-the-gallery"></a>从库添加 Fieldglass
要配置 Fieldglass 与 Azure AD 的集成，需要从库将Fieldglass 添加到托管 SaaS 应用列表。

**若要从库添加 Fieldglass，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在搜索框中，键入 **Fieldglass**。

    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/tutorial_fieldglass_search.png)

1. 在结果窗格中，选择“Fieldglass”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/tutorial_fieldglass_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Fieldglass 配置和测试 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Fieldglass 用户。 换句话说，需要在 Azure AD 用户与 Fieldglass 中的相关用户间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Fieldglass 中“用户名”的值来建立此链接关系。

若要配置和测试 Fieldglass 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Fieldglass 测试用户](#creating-a-fieldglass-test-user)** - 在 Fieldglass 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录，并在 Fieldglass 应用程序中配置单一登录。

**若要配置 Fieldglass 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Fieldglass”应用程序集成页上，单击“单一登录” 。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/fieldglass-tutorial/tutorial_fieldglass_samlbase.png)

1. 在“Fieldglass 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/fieldglass-tutorial/tutorial_fieldglass_url.png)

    a. 在“标识符”文本框中，键入 URL `https://www.fieldglass.com`，或键入使用以下模式的 URL：`https://<company name>.fgvms.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://www.fieldglass.net/<company name>`|
    | `https://<company name>.fgvms.com/<company name>`|

    > [!NOTE] 
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Fieldglass 支持团队](http://www.fieldglass.com/solutions/support)获取这些值。
 
1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/fieldglass-tutorial/tutorial_fieldglass_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/fieldglass-tutorial/tutorial_general_400.png)

1. 在“Fieldglass 配置”部分中，单击“配置 Fieldglass”打开“配置登录”窗口。 从“快速参考”部分复制“注销 URL 和 SAML 实体 ID”。

    ![配置单一登录](./media/fieldglass-tutorial/tutorial_fieldglass_configure.png) 

1. 若要在“Fieldglass”端配置单一登录，需要将下载的“证书(Base64)”和“注销 URL、SAML 实体 ID”发送给 [Fieldglass 支持团队](http://www.fieldglass.com/solutions/support) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/fieldglass-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-fieldglass-test-user"></a>创建 Fieldglass 测试用户

本部分的目的是在 FieldGlass 中创建名为 Britta Simon 的用户。 请与 [Fieldglass 支持团队](http://www.fieldglass.com/solutions/support)协作，将用户添加到 Fieldglass 帐户户中。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Fieldglass 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Fieldglass，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Fieldglass”。

    ![配置单一登录](./media/fieldglass-tutorial/tutorial_fieldglass_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

在访问面板中单击 Fieldglass 磁贴时，应该会自动登录 Fieldglass 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/fieldglass-tutorial/tutorial_general_04.png

[100]: ./media/fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/fieldglass-tutorial/tutorial_general_201.png
[202]: ./media/fieldglass-tutorial/tutorial_general_202.png
[203]: ./media/fieldglass-tutorial/tutorial_general_203.png

