---
title: 教程：Azure Active Directory 与 Bonusly 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Bonusly 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 175c00d36491fbf43149aef9a590219b330581c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422971"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>教程：Azure Active Directory 与 Bonusly 集成

本教程介绍了如何将 Bonusly 与 Azure Active Directory (Azure AD) 集成。

将 Bonusly 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Bonusly
- 可以让用户使用其 Azure AD 帐户自动登录到 Bonusly（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Bonusly 的集成，需具备以下项：

- Azure AD 订阅
- 已启用 Bonusly 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Bonusly
1. 配置和测试 Azure AD 单一登录

## <a name="adding-bonusly-from-the-gallery"></a>从库中添加 Bonusly
要配置 Bonusly 与 Azure AD 的集成，需要从库中将 Bonusly 添加到托管 SaaS 应用列表。

**若要从库中添加 Bonusly，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Bonusly”，在结果面板中选择“Bonusly”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Bonusly](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Bonusly 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Bonusly 用户。 换句话说，需要建立 Azure AD 用户与 Bonusly 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Bonusly 中“用户名”的值来建立此链接关系。

若要配置和测试 Bonusly 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Bonusly 测试用户](#create-a-bonusly-test-user)** - 在 Bonusly 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Bonusly 应用程序中配置单一登录。

**若要配置 Bonusly 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的 **Bonusly** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. 在“Bonusly 域和 URL”部分中，执行以下步骤：

    ![Bonusly 域和 URL 单一登录信息](./media/bonus-tutorial/tutorial_bonusly_url.png)

    在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Bonusly 支持团队](https://bonus.ly/contact)获取此值。
 
1. 在“SAML 签名证书”部分中，复制证书的指纹值。

    ![证书下载链接](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/bonus-tutorial/tutorial_general_400.png)

1. 在“Bonusly 配置”部分，单击“配置 Bonusly”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Bonusly 配置](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. 在另一个浏览器窗口中，登录到 **Bonusly** 租户。

1. 在顶部工具栏中，单击“设置”，并选择“集成和应用”。
   
    ![“Bonusly 社交”部分](./media/bonus-tutorial/ic773686.png "Bonusly")
1. 在“单一登录”下选择“SAML”。

1. 在“SAML”对话框页上执行以下步骤：
   
    ![“Bonusly Saml”对话框页](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. 在“IdP SSO 目标 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
   
    b. 在“IdP 颁发者”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。 

    c. 在“IdP 登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    d. 将从 Azure 门户复制的“指纹”值粘贴到“证书指纹”文本框中。
   
1. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](./media/bonus-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/bonus-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![“添加”按钮](./media/bonus-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![“用户”对话框](./media/bonus-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="create-a-bonusly-test-user"></a>创建 Bonusly 测试用户

为了使 Azure AD 用户能够登录 Bonusly，必须将其预配到 Bonusly 中。 就 Bonusly 来说，预配任务需要手动完成。

>[!NOTE]
>可使用其他任何 Bonusly 用户帐户创建工具或 Bonusly 提供的 API 预配 AAD 用户帐户。
>  

**若要配置用户设置，请执行以下步骤：**

1. 在 Web 浏览器窗口中，登录到 Bonusly 租户。

1. 单击“设置”。
 
    ![设置](./media/bonus-tutorial/ic781041.png "设置")

1. 单击“用户和奖金”选项卡。
   
    ![用户和奖金](./media/bonus-tutorial/ic781042.png "用户和奖金")

1. 单击“管理用户”。
   
    ![管理用户](./media/bonus-tutorial/ic781043.png "管理用户")

1. 单击“添加用户”。
   
    ![添加用户](./media/bonus-tutorial/ic781044.png "添加用户")

1. 在“添加用户”对话框中，执行以下步骤：
   
    ![添加用户](./media/bonus-tutorial/ic781045.png "添加用户")  

    a. 在“名字”文本框中，输入用户的名字（如“Britta”）。

    b. 在“姓氏”文本框中，输入用户的姓氏（如“Simon”）。
 
    c. 在“电子邮件”文本框中，输入用户的电子邮件地址（如 brittasimon@contoso.com）。

    d. 单击“ **保存**”。
   
     >[!NOTE]
     >Azure AD 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
     >  

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Bonusly 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Bonusly，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Bonusly”。

    ![应用程序列表中的 Bonusly 链接](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

在访问面板中单击“Bonusly”磁贴时，应该会自动登录到 Bonusly 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

