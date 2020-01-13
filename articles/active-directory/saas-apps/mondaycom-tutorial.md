---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 monday.com 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 monday.com 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7126b6e40c7d76244ec5cd62566c4bad6dc0529
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430912"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>教程：Azure Active Directory 单一登录 (SSO) 与 monday.com 的集成

本教程介绍如何将 monday.com 与 Azure Active Directory (Azure AD) 集成。 将 monday.com 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 monday.com。
* 让用户使用其 Azure AD 帐户自动登录到 monday.com。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 monday.com 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* monday.com 支持“SP 和 IDP”发起的 SSO 
* monday.com 支持恰时用户预配 

## <a name="adding-mondaycom-from-the-gallery"></a>从库中添加 monday.com

要配置 monday.com 与 Azure AD 的集成，需要从库中将 monday.com 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“monday.com”   。
1. 从结果面板中选择“monday.com”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>配置并测试 monday.com 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 monday.com 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 monday.com 相关用户之间建立链接关系。

若要配置和测试 monday.com 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 monday.com SSO](#configure-mondaycom-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 monday.com 测试用户](#create-mondaycom-test-user)** - 在 monday.com 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“monday.com”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果已获取“服务提供商元数据文件”并想要在“IDP”发起的模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤    ：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分。  

    ![image](common/idp-intiated.png)

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请手动填充这些值。   “标识符”和“回复 URL”相同，值采用以下模式：`https://<your-domain>.monday.com/saml/saml_callback`  

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![image](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [monday.com 客户端支持团队](mailto:dev@food.ee)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. monday.com 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，monday.com 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | 电子邮件 | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 monday.com”部分，根据要求复制相应的 URL  。

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

在本部分，我们通过授予 B.Simon 访问 monday.com 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“monday.com”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-mondaycom-sso"></a>配置 monday.com SSO

1. 若要在 monday.com 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 monday.com”  会定向到 monday.com 应用程序。 在此处，请提供管理员凭据以登录到 monday.com。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 monday.com，请打开新的 Web 浏览器窗口，以管理员身份登录 monday.com，并执行以下步骤：

1. 转到页面右上角的“配置文件”，然后单击“管理员”   。

    ![monday.com 配置](./media/mondaycom-tutorial/configuration01.png)

1. 选择“安全性”，并确保单击“SAML”旁边的“打开”   。

    ![monday.com 配置](./media/mondaycom-tutorial/configuration02.png)

1. 填写 IDP 中的以下详细信息。

    ![monday.com 配置](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >有关更多详细信息，请参阅[此文](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642)

### <a name="create-mondaycom-test-user"></a>创建 monday.com 测试用户

本部分将在 monday.com 中创建一个名为 B.Simon 的用户。 monday.com 支持默认启用的实时预配。 此部分不存在任何操作项。 尝试访问 monday.com 时，如果 monday.com 中尚不存在用户，则系统会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 monday.com 磁贴时，应会自动登录到为其设置了 SSO 的 monday.com。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [试用带有 Azure AD 的 monday.com](https://aad.portal.azure.com/)