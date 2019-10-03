---
title: 教程：Azure Active Directory 与 dmarcian 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 dmarcian 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823700"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>教程：将 dmarcian 与 Azure Active Directory 集成

本教程介绍如何将 dmarcian 与 Azure Active Directory (Azure AD) 集成。 将 dmarcian 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 dmarcian。
* 让用户使用其 Azure AD 帐户自动登录到 dmarcian。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 dmarcian 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* dmarcian 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-dmarcian-from-the-gallery"></a>从库中添加 dmarcian

若要配置 dmarcian 与 Azure AD 的集成，需要从库中将 dmarcian 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“dmarcian”   。
1. 从结果面板中选择“dmarcian”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 dmarcian 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 dmarcian 相关用户之间建立链接关系。

若要配置和测试 dmarcian 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 dmarcian SSO](#configure-dmarcian-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 dmarcian 测试用户](#create-dmarcian-test-user)** - 在 dmarcian 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“dmarcian”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”  部分中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL： 

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL： 
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新这些值。

4. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>配置 dmarcian SSO

1. 若要在 dmarcian 中自动执行配置，需要通过单击“安装扩展”来安装**我的应用安全登录浏览器扩展**  。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 dmarcian”会定向到 dmarcian 应用程序  。 在此处，提供管理员凭据以登录到 dmarcian。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 dmarcian，请打开新的 Web 浏览器窗口，以管理员身份登录 dmarcian 公司站点，并执行以下步骤：

4. 单击右上角的“配置文件”，并导航到“首选项”。  

    ![首选项](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. 向下滚动并单击“单一登录”部分，然后单击“配置”。  

    ![单人](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. 在“SAML 单一登录”页上，将“状态”设置为“已启用”并执行以下步骤：   

    ![身份验证](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * 在“将 dmarcian 添加到标识提供者”部分，单击“复制”以复制实例的“断言使用者服务 URL”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“回复 URL”文本框中。     

    * 在“将 dmarcian 添加到标识提供者”部分，单击“复制”以复制实例的“实体 ID”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“标识符”文本框中。     

    * 在“设置身份验证”部分的“标识提供者元数据”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”。   

    * 在“设置身份验证”部分的“属性语句”文本框中，粘贴 URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`  

    * 在“设置登录 URL”部分，复制实例的“登录 URL”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“登录 URL”文本框中。    

        > [!Note]
        > 可以根据组织的情况修改“登录 URL”。 

    * 单击“ **保存**”。

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

在本部分中，将通过授予 B.Simon 访问 dmarcian 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“dmarcian”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-dmarcian-test-user"></a>创建 dmarcian 测试用户

要使 Azure AD 用户能够登录到 dmarcian，必须将其预配到 dmarcian 中。 在 dmarcian 中，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 dmarcian。

2. 单击右上角的“配置文件”，并导航到“管理用户”。  

    ![用户](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. 在“SSO 用户”部分的右侧，单击“添加新用户”。  

    ![添加用户](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. 在“添加新用户”弹出窗口中执行以下步骤： 

    ![新用户](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. 在“新建用户电子邮件”文本框中，输入用户的电子邮件，例如 brittasimon\@contoso.com   。

    b. 若要向该用户授予管理员权限，请选择“将用户设为管理员”。 

    c. 单击“添加用户”  。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 dmarcian 磁贴时，应当会自动登录到为其设置了 SSO 的 dmarcian。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

