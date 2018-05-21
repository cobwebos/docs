---
title: 教程：Azure Active Directory 与 Velpic SAML 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Velpic SAML 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 03ef1ef04d80ac9bb83bcce2082b6cc3f617d812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>教程：Azure Active Directory 与 Velpic SAML 集成

本教程介绍如何将 Velpic SAML 与 Azure Active Directory (Azure AD) 集成。

将 Velpic SAML 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Velpic SAML
- 可以让用户通过其 Azure AD 帐户自动登录到 Velpic SAML（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Velpic SAML 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Velpic SAML 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Velpic SAML
2. 配置和测试 Azure AD 单一登录

## <a name="adding-velpic-saml-from-the-gallery"></a>从库添加 Velpic SAML
要配置 Velpic SAML 与 Azure AD 的集成，需要从库中将 Velpic SAML 添加到托管 SaaS 应用列表。

**若要从库中添加 Velpic SAML，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Velpic SAML”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. 在结果窗格中，选择“Velpic SAML”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Velpic SAML 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Velpic SAML 用户。 换句话说，需要建立 Azure AD 用户与 Velpic SAML 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Velpic SAML 中“用户名”的值来建立此链接关系。

若要配置并测试 Velpic SAML 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Velpic SAML 测试用户](#creating-a-velpic-saml-test-user)** - 在 Velpic SAML 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 管理门户中启用 Azure AD 单一登录并在 Velpic SAML 应用程序中配置单一登录。

**若要配置 Velpic SAML 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Velpic SAML”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. 将详细信息输入“Velpic SAML 域和 URL”部分。

    ![配置单一登录](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. 在“登录 URL”文本框中，键入 `https://<sub-domain>.velpicsaml.net` 值

    b. 在“标识符”文本框中，粘贴“单一登录 URL”值 `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > 请注意，“登录 URL”由 Velpic SAML 团队提供，而“标识符”值会在完成 Velpic SAML 侧的 SSO 插件配置后可供使用。 需要从 Velpic SAML 应用程序页复制该值，将其粘贴到此处。

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. 在“Velpic SAML 配置”部分中，单击“配置 Velpic SAML”打开“配置登录”窗口。 从“快速参考”部分复制 SAML 实体 ID。

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Velpic SAML 公司站点。

8. 单击“管理”选项卡，转到“集成”部分，在这里需单击“插件”按钮，创建用于登录的新插件。

    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. 单击“添加插件”按钮。
    
    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. 单击“添加插件”页中的“SAML”磁贴。
    
    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. 输入新 SAML 插件的名称，并单击“添加”按钮。

    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. 输入详细信息，如下所示：

    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. 在“名称”文本框中，键入 SAML 插件的名称。

    b. 在“颁发者 URL”文本框中，粘贴“SAML 实体 ID”，该 ID 是从 Azure 门户的“配置登录”窗口复制的。

    c. 在“提供程序元数据配置”中，上传从 Azure 门户下载的元数据 XML 文件。

    d. 也可通过启用“自动创建新用户”复选框，来选择启用 SAML 实时预配。 如果用户不存在于 Velpic 中且该标记未启用，则从 Azure 登录会失败。 如果该标记已启用，则用户会在登录时自动预配到 Velpic 中。 

    e. 从文本框复制“单一登录 URL”，然后将其粘贴到 Azure 门户中。
    
    f. 单击“ **保存**”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-velpic-saml-test-user"></a>创建 Velpic SAML 测试用户

此步骤通常不是必需的，因为应用程序支持实时用户预配。 如果未启用自动用户预配，则可手动创建用户，如下所述。

以管理员身份登录到 Velpic SAML 公司站点，并执行以下步骤：
    
1. 单击“管理”选项卡，转到“用户”部分，并单击“新建”按钮添加用户。

    ![添加用户](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. 在“新建用户”对话框页中，执行以下步骤。

    ![user](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. 在“名字”文本框中，键入 Britta Simon 的名字。

    b. 在“姓氏”文本框中，键入 Britta Simon 的姓氏。

    c. 在“用户名”文本框中，键入 Britta Simon 的用户名。

    d. 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    e. 其余信息为可选，可根据需要填写。
    
    f. 单击“保存”。  

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Velpic SAML 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Velpic SAML，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Velpic SAML”。

    ![配置单一登录](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 单击访问面板中的 Velpic SAML 磁贴时，应显示 Velpic SAML 应用程序的登录页。 应该在登录页上显示“使用 Azure AD 登录”按钮。

    ![插件](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. 单击“使用 Azure AD 登录”按钮，使用 Azure AD 帐户登录到 Velpic。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

