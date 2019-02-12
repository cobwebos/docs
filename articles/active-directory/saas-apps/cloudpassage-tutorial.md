---
title: 教程：Azure Active Directory 与 CloudPassage 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 CloudPassage 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 0e6ffb7f797746fed30520c1c99130ff585909a9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697853"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>教程：Azure Active Directory 与 CloudPassage 集成

本教程介绍如何将 CloudPassage 与 Azure Active Directory (Azure AD) 集成。
将 CloudPassage 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 CloudPassage。
* 可让用户使用其 Azure AD 帐户自动登录到 CloudPassage（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 CloudPassage 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 CloudPassage 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* CloudPassage 支持 **SP** 发起的 SSO

## <a name="adding-cloudpassage-from-the-gallery"></a>从库中添加 CloudPassage

要配置 CloudPassage 与 Azure AD 的集成，需要从库中将 CloudPassage 添加到托管 SaaS 应用列表。

**若要从库中添加 CloudPassage，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“CloudPassage”，在结果面板中选择“CloudPassage”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 CloudPassage](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分将基于一个名为“Britta Simon”的测试用户，配置和测试 CloudPassage 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 CloudPassage 相关用户之间建立链接关系。

若要配置并测试 CloudPassage 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 CloudPassage 单一登录](#configure-cloudpassage-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 CloudPassage 测试用户](#create-cloudpassage-test-user)** - 在 CloudPassage 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 CloudPassage 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“CloudPassage”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![CloudPassage 域和 URL 单一登录信息](common/sp-reply.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://portal.cloudpassage.com/saml/init/accountid`。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://portal.cloudpassage.com/saml/consume/accountid`。 可以通过在 CloudPassage 门户的“单一登录设置”部分中单击“SSO 设置文档”来获取此属性的值。

    ![配置单一登录](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 请联系 [CloudPassage 客户端支持团队](https://www.cloudpassage.com/company/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. CloudPassage 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤： 

    | Name | 源属性|
    | ---------------| --------------- |
    | 名 |user.givenname |
    | 姓 |user.surname |
    | 电子邮件 |user.mail |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

7. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 CloudPassage”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-cloudpassage-single-sign-on"></a>配置 CloudPassage 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 CloudPassage 公司站点。

1. 在顶部菜单中，单击“设置”，并单击“网站管理”。 
   
    ![配置单一登录][12]

1. 单击“身份验证设置”选项卡。 
   
    ![配置单一登录][13]

1. 在“单一登录设置”部分中，执行以下步骤： 
   
    ![配置单一登录][14]

    a. 选中“启用单一登录 (SSO)(SSO 设置文档)”复选框。
    
    b. 将“Azure AD 标识符”粘贴到“SAML 颁发者 URL”文本框。
  
    c. 将“登录 URL”粘贴到“SAML 终结点 URL”文本框。
  
    d. 将“注销 URL”粘贴到“注销登陆页面”文本框。
  
    e. 在记事本中打开下载的证书，将下载证书的内容复制到剪贴板，然后将其粘贴到“x 509 证书”文本框。
  
    f. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过授予 Britta Simon 访问 CloudPassage 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“CloudPassage”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“CloudPassage”。

    ![应用程序列表中的 CloudPassage 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-cloudpassage-test-user"></a>创建 CloudPassage 测试用户

本部分的目的是在 CloudPassage 中创建名为 Britta Simon 的用户。

**若要在 CloudPassage 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 **CloudPassage** 公司站点。 

1. 在顶部工具栏中，单击“设置”，并单击“网站管理”。 
   
    ![创建 CloudPassage 测试用户][22] 

1. 单击“用户”选项卡，并单击“添加新用户”。 
   
    ![创建 CloudPassage 测试用户][23]

1. 在“添加新用户”部分中，执行以下步骤： 
   
    ![创建 CloudPassage 测试用户][24]
    
    a. 在“名字”文本框中，键入“Britta”。 
  
    b. 在“姓氏”文本框中，键入“Simon”。
  
    c. 在“用户名”文本框、“电子邮件”文本框和“重新键入电子邮件”文本框中，键入 Britta 在 Azure AD 中的用户名。
  
    d. 对于“访问类型”，选择“启用 Halo 门户访问”。
  
    e. 单击“添加”。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 CloudPassage 磁贴时，应当会自动登录到为其设置了 SSO 的 CloudPassage。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

