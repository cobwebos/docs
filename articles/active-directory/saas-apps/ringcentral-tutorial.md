---
title: 教程：Azure Active Directory 与 RingCentral 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 RingCentral 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092863"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>教程：将 RingCentral 与 Azure Active Directory 集成

本教程介绍如何将 RingCentral 与 Azure Active Directory (Azure AD) 集成。 将 RingCentral 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 RingCentral。
* 让用户使用其 Azure AD 帐户自动登录到 RingCentral。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用单一登录 (SSO) 的 RingCentral 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 RingCentral 支持 **IDP** 发起的 SSO

## <a name="adding-ringcentral-from-the-gallery"></a>从库中添加 RingCentral

若要配置 RingCentral 与 Azure AD 的集成，需要从库中将 RingCentral 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“RingCentral”   。
1. 从结果面板中选择“RingCentral”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 RingCentral 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 RingCentral 相关用户之间建立链接关系。

若要配置和测试 RingCentral 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 RingCentral SSO](#configure-ringcentral-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 RingCentral 测试用户](#create-ringcentral-test-user)** - 在 RingCentral 中创建 Britta Simon 的对应用户，并将其与用户的 Azure AD 身份关联。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“RingCentral”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    1. 单击“上传元数据文件”  。
    1. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 
    1. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分    。

    > [!Note]
    > 请在本教程稍后将介绍的“RingCentral SSO 配置”页面上获取**服务提供程序元数据文件**。

1. 如果没有**服务提供程序元数据文件**，请输入以下字段的值：

    a. 在“标识符”文本框中，键入一个 URL  ：

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. 在“回复 URL”  文本框中键入 URL：

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>配置 RingCentral SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 RingCentral。

1. 在顶部单击“工具”。 

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. 导航到“单一登录”  。

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. 在“单一登录”  页的“SSO 配置”  部分，在“步骤 1”中单击“编辑”，执行以下步骤：  

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. 在“设置单一登录”  页上，执行以下步骤：

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. 单击“浏览”，上传从 Azure 门户下载的元数据文件。 

    b. 上传元数据以后，值会自动填充在“SSO 常规信息”部分中。 

    c. 在“属性映射”部分，针对“将电子邮件属性映射到”选项选择 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`  

    d. 单击“ **保存**”。

    e. 在“步骤 2”中单击“下载”以下载“服务提供程序元数据文件”，并在“基本 SAML 配置”部分上传该文件，以便在 Azure 门户中自动填充“标识符”和“回复 URL”值       。

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 在同一页导航到“启用 SSO”部分，  执行以下步骤：

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * 选择“启用 SSO 服务”。 

    * 选择“允许用户使用 SSO 或 RingCentral 凭据登录”。 

    * 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 RingCentral 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“RingCentral”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-ringcentral-test-user"></a>创建 RingCentral 测试用户

在本部分，请在 RingCentral 中创建一个名为“Britta Simon”的用户。 请与 [RingCentral 客户端支持团队](https://success.ringcentral.com/RCContactSupp) 协作，在 RingCentral 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“RingCentral”磁贴时，应会自动登录到设置了 SSO 的 RingCentral。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
