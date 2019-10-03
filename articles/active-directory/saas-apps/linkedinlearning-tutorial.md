---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 LinkedIn Learning 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LinkedIn Learning 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cf5f8d79c2f416ea0c00064fecc8fd6008ae047
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71119847"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>教程：Azure Active Directory 单一登录 (SSO) 与 LinkedIn Learning 集成

本教程介绍如何将 LinkedIn Learning 与 Azure Active Directory (Azure AD) 集成。 将 LinkedIn Learning 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LinkedIn Learning。
* 让用户使用其 Azure AD 帐户自动登录到 LinkedIn Learning。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 LinkedIn Learning 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* LinkedIn Learning 支持 SP 和 IDP 发起的 SSO 
* LinkedIn Learning 支持实时用户预配 

## <a name="adding-linkedin-learning-from-the-gallery"></a>从库中添加 LinkedIn Learning

要配置 LinkedIn Learning 与 Azure AD 的集成，需要从库中将 LinkedIn Learning 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **LinkedIn Learning**。 
1. 在结果面板中选择“LinkedIn Learning”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>配置和测试 LinkedIn Learning 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 LinkedIn Learning 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 LinkedIn Learning 相关用户之间建立链接关系。

若要配置和测试 LinkedIn Learning 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 LinkedIn Learning SSO](#configure-linkedin-learning-sso)** - 在应用程序端配置单一登录。
    1. **[创建 LinkedIn Learning 测试用户](#create-linkedin-learning-test-user)** - 在 LinkedIn Learning 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“LinkedIn Learning”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

     a. 在“标识符”文本框中，输入从 LinkedIn 门户复制的“实体 ID”   。 

    b. 在“回复 URL”文本框中，输入从 LinkedIn 门户复制的“断言使用者服务(ACS) URL”   。

    c. 如果想在 SP 发起的模式下配置应用程序，请单击将在其中指定登录 URL 的“基本 SAML 配置”部分中的“设置其他 URL”选项    。 若要创建登录 URL，请复制断言使用者服务 (ACS) URL 并将 /saml/ 替换为 /login/  。 执行该操作后，单一登录 URL 应包含以下模式：

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将在“配置 LinkedIn Learning SSO”部分中介绍如何使用实际标识符和回复 URL 来更新这些值  。

1. LinkedIn Learning 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 LinkedIn Learning 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。 

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 LinkedIn Learning”部分中，根据要求复制相应的 URL。 

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

在本部分中，将通过授予 B.Simon 访问 LinkedIn Learning 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“LinkedIn Learning”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-linkedin-learning-sso"></a>配置 LinkedIn Learning SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 LinkedIn Learning 租户。

2. 在“帐户中心”  ，单击“设置”  下的“全局设置”  。 此外，从下拉列表选择“学习 - 默认”  。

    ![配置单一登录](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. 单击“或单击此处从窗体加载和复制单个字段”，并复制“实体 ID”和“断言使用者服务(ACS) URL”，然后将其粘贴在 Azure 门户的“基本 SAML 配置”部分     。

    ![配置单一登录](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. 转到“LinkedIn 管理设置”  分区。 单击“上传 XML 文件”选项，上传已从 Azure 门户下载的 XML 文件。 

    ![配置单一登录](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. 单击“打开”  启用 SSO。 SSO 状态将从“未连接”  更改为“已连接” 

    ![配置单一登录](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>创建 LinkedIn Learning 测试用户

LinkedIn Learning 应用程序支持实时用户设置，且进行身份验证后，会在应用程序中自动创建用户。 在 LinkedIn Learning 门户的管理设置页上，将“自动分配许可证”  开关切换为活动状态实时预配，此操作还会将许可证分配给用户。

   ![创建 Azure AD 测试用户](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 LinkedIn Learning 磁贴时，应当会自动登录到为其设置了 SSO 的 LinkedIn Learning。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 LinkedIn Learning](https://aad.portal.azure.com/)

