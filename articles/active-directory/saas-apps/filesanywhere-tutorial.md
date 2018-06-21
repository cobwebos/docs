---
title: 教程：Azure Active Directory 与 FilesAnywhere 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FilesAnywhere 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 8d80e537bc031a777ae037f5147b2c84b7e76281
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215964"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>教程：Azure Active Directory 与 FilesAnywhere 集成

在本教程中，了解如何将 FilesAnywhere 与 Azure Active Directory (Azure AD) 集成。

将 FilesAnywhere 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 FilesAnywhere
- 可以让用户使用其 Azure AD 帐户自动登录到 FilesAnywhere（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 FilesAnywhere 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 FilesAnywhere 单一登录的订阅


> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。


测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 FilesAnywhere
2. 配置和测试 Azure AD 单一登录


## <a name="adding-filesanywhere-from-the-gallery"></a>从库中添加 FilesAnywhere
要配置 FilesAnywhere 与 Azure AD 的集成，需要从库中将 FilesAnywhere 添加到托管 SaaS 应用列表。

**若要从库中添加 FilesAnywhere，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“FilesAnywhere”。

    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

5. 在结果面板中，选择“FilesAnywhere”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 FilesAnywhere 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 FilesAnywhere 用户。 换句话说，需要建立 Azure AD 用户与 FilesAnywhere 中相关用户之间的关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 FilesAnywhere 中“用户名”的值来建立此关联关系。

若要配置和测试 FilesAnywhere 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 FilesAnywhere 测试用户](#creating-a-filesanywhere-test-user)** - 在 FilesAnywhere 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
3. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
4. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 管理门户中启用 Azure AD 单一登录并在 FilesAnywhere 应用程序中配置单一登录。

**若要配置 FilesAnywhere 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“FilesAnywhere”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

3. 在“FilesAnywhere 域和 URL”部分中，如果要在“IDP 启动的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> 请注意，“clientid”的值“215”只是一个示例。 需要将其替换为实际的 clientid 值。

4. 在“FilesAnywhere 域和 URL”部分中，如果要在“SP 启动的模式”下配置应用程序，请执行以下步骤：
    
    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. 单击“显示高级 URL 设置”选项

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和回复 URL 更新这些值。 若要获取这些值，请联系 [FilesAnywhere 支持团队](mailto:support@FilesAnywhere.com)。 

5. FilesAnywhere Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    当用户注册 FilesAnywhere 时，他们会从 [FilesAnywhere 团队](mailto:support@FilesAnywhere.com)那里获得“clientid”属性的值。 必须为“Client ID”属性添加由 FilesAnywhere 提供的唯一值。 上面所示的所有这些属性都是必需的。
    > [!NOTE] 
    > 请注意，“clientid”的值“2331”只是一个示例。 需要提供实际的值。


6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ---------------| --------------- |    
    | clientid | "uniquevalue" |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定”

7. 单击“保存”按钮。

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_general_400.png)

8. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

9. 在“FilesAnywhere 配置”部分中，单击“配置 FilesAnywhere”打开“配置登录”窗口。

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

10. 若要在 FilesAnywhere 端完成应用程序的 SSO 配置，请与 [FilesAnywhere 支持团队](mailto:support@FilesAnywhere.com)联系，并向其提供下载的 SAML 令牌签名证书和单一登录 (SSO) URL。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。 



### <a name="creating-a-filesanywhere-test-user"></a>创建 FilesAnywhere 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 FilesAnywhere 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 FilesAnywhere，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“FilesAnywhere”。

    ![配置单一登录](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 FilesAnywhere 磁贴时，应该会自动登录到 FilesAnywhere 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
