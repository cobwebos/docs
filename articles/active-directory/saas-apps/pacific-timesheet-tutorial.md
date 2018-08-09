---
title: 教程：Azure Active Directory 与 Pacific Timesheet 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Pacific Timesheet 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e546e8ba-821a-4942-9545-c84b0670beab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 051e39d0e7a58c79eb00bc3dfb73eaf5389617c8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448357"
---
# <a name="tutorial-azure-active-directory-integration-with-pacific-timesheet"></a>教程：Azure Active Directory 与 Pacific Timesheet 的集成

本教程介绍如何将 Pacific Timesheet 与 Azure Active Directory (Azure AD) 集成。

将 Pacific Timesheet 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Pacific Timesheet
- 可以让用户通过其 Azure AD 帐户自动登录到 Pacific Timesheet（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Pacific Timesheet 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Pacific Timesheet 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Pacific Timesheet
1. 配置和测试 Azure AD 单一登录

## <a name="adding-pacific-timesheet-from-the-gallery"></a>从库中添加 Pacific Timesheet
要通过配置将 Pacific Timesheet 集成到 Azure AD 中，需从库将 Pacific Timesheet 添加到托管式 SaaS 应用的列表中。

**若要从库添加 Pacific Timesheet，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在搜索框中，键入“Pacific Timesheet”。

    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_search.png)

1. 在结果面板中，选择“Pacific Timesheet”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 Pacific Timesheet 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解与 Azure AD 中的用户相对应的 Pacific Timesheet 中的用户是谁。 换句话说，需要建立 Azure AD 用户与 Pacific Timesheet 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 Pacific Timesheet 中“用户名”的值来建立此关联关系。

若要使用 Pacific Timesheet 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 Pacific Timesheet 测试用户](#creating-a-pacific-timesheet-test-user) - 在 Pacific Timesheet 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Pacific Timesheet 应用程序中配置单一登录。

**若要通过 Pacific Timesheet 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Pacific Timesheet 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_samlbase.png)

1. 在“Pacific Timesheet 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Pacific Timesheet 客户端支持团队](http://www.pacifictimesheet.com/support)获取这些值。
 
1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_general_400.png)

1. 在“Pacific Timesheet 配置”部分中，单击“配置 Pacific Timesheet”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_configure.png) 

1. 若要在 Pacific Timesheet 端配置单一登录，需要将下载的证书 (Base64)、SAML 单一登录服务 URL 和 SAML 实体 ID 发送给 [Pacific Timesheet 支持团队](http://www.pacifictimesheet.com/support)。 他们会对此进行设置，使 SAML SSO 连接在两端均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/pacific-timesheet-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-pacific-timesheet-test-user"></a>创建 Pacific Timesheet 测试用户

本部分需在 Pacific Timesheet 中创建名为“Britta Simon”的用户。 请在 [Pacific Timesheet 支持团队](http://www.pacifictimesheet.com/support)的协助下，在应用程序中创建用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分需授予 Britta Simon 访问 Pacific Timesheet 的权限，使之能够使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Pacific Timesheet，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中选择“Pacific Timesheet”。

    ![配置单一登录](./media/pacific-timesheet-tutorial/tutorial_pacifictimesheet_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的“Pacific Timesheet”磁贴时，用户就会自动登录到 Pacific Timesheet 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pacific-timesheet-tutorial/tutorial_general_01.png
[2]: ./media/pacific-timesheet-tutorial/tutorial_general_02.png
[3]: ./media/pacific-timesheet-tutorial/tutorial_general_03.png
[4]: ./media/pacific-timesheet-tutorial/tutorial_general_04.png

[100]: ./media/pacific-timesheet-tutorial/tutorial_general_100.png

[200]: ./media/pacific-timesheet-tutorial/tutorial_general_200.png
[201]: ./media/pacific-timesheet-tutorial/tutorial_general_201.png
[202]: ./media/pacific-timesheet-tutorial/tutorial_general_202.png
[203]: ./media/pacific-timesheet-tutorial/tutorial_general_203.png

