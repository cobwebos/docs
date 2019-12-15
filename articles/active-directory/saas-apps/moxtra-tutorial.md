---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Moxtra 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Moxtra 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889543"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Moxtra 的集成

本教程介绍如何将 Moxtra 与 Azure Active Directory (Azure AD) 集成。 将 Moxtra 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Moxtra。
* 让用户使用其 Azure AD 帐户自动登录到 Moxtra。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Moxtra 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Moxtra 支持 SP 发起的 SSO 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-moxtra-from-the-gallery"></a>从库中添加 Moxtra

要配置 Moxtra 与 Azure AD 的集成，需要从库中将 Moxtra 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Moxtra**。 
1. 在结果面板中选择“Moxtra”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>配置并测试 Moxtra 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Moxtra 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Moxtra 中的相关用户之间建立链接关系。

若要配置并测试 Moxtra 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Moxtra SSO](#configure-moxtra-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Moxtra 测试用户](#create-moxtra-test-user)** - 在 Moxtra 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Moxtra”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    在“登录 URL”文本框中，键入 URL：`https://www.moxtra.com/service/#login` 

1. Moxtra 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Moxtra 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以添加 SAML 令牌属性，如下表中所示：

    | 名称 | 源属性|
    | ------------------- | -------------------- |    
    | 名 | user.givenname |
    | 姓 | user.surname |
    | idpid    | < Azure AD 标识符 >

    > [!Note]
    > idpid 属性的值不是真实值  。 在步骤 8 中，可以从“设置 Moxtra”获取实际值。  

    1. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    1. 在“名称”文本框中，键入为该行显示的属性名称。 

    1. 将“命名空间”留空  。

    1. 选择“源”作为“属性”  。

    1. 在“源属性”  列表中，键入为该行显示的属性值。

    1. 单击“确定” 

    1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Moxtra”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Moxtra 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Moxtra”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-moxtra-sso"></a>配置 Moxtra SSO

1. 在另一个浏览器窗口中，以管理员身份登录到 Moxtra 公司站点。

2. 在左侧工具栏上，单击“管理控制台”>“SAML 单一登录”，然后单击“新建”。  
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. 在 **SAML** 页上执行以下步骤：
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. 在“名称”文本框中，键入配置名称（例如：  SAML）  。 
  
    b. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。 
 
    c. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。 
 
    d. 在“AuthnContextClassRef”文本框中，键入“urn:oasis:names:tc:SAML:2.0:ac:classes:Password”。   
 
    e. 在“NameID 格式”  文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress”  。 
 
    f. 在记事本中打开从 Azure 门户下载的证书，复制内容，然后粘贴到“证书”文本框中  。    
 
    g. 在“SAML 电子邮件域”文本框中，键入 SAML 电子邮件域。    
  
    >[!NOTE]
    >若要查看用来验证域的步骤，请单击下方的“**i**”。

    h. 单击“更新”  。

### <a name="create-moxtra-test-user"></a>创建 Moxtra 测试用户

本部分的目标是在 Moxtra 中创建一个名为 B.Simon 的用户。

**若要在 Moxtra 中创建名为 B.simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 Moxtra 公司站点。

1. 在左侧工具栏上，单击“管理控制台”>“用户管理”，并单击“添加用户”。  
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. 在“添加用户”  对话框中，执行以下步骤：
  
    a. 在“名字”文本框中，键入 **B**。 
  
    b. 在“姓氏”文本框中，键入“Simon”。  
  
    c. 在“电子邮件”文本框中，键入 B.simon 在 Azure 门户中使用的电子邮件地址。 
  
    d. 在“分部”文本框中，键入“Dev”。  
  
    e. 在“部门”文本框中，键入“IT”。  
  
    f. 选择“管理员”  。
  
    g. 单击“添加”  。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Moxtra 磁贴时，应当会自动登录到已为其设置了 SSO 的 Moxtra。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Moxtra](https://aad.portal.azure.com/)

