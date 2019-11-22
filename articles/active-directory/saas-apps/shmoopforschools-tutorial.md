---
title: 教程：Azure Active Directory 与 Shmoop For Schools 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Shmoop For Schools 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ce2e51f72b9b4f709bf26ce6d92ad3861d36db
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081649"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>教程：将 Shmoop For Schools 与 Azure Active Directory 集成

本教程介绍如何将 Shmoop For Schools 与 Azure Active Directory (Azure AD) 集成。 将 Shmoop For Schools 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权限访问 Shmoop For Schools。
* 让用户使用其 Azure AD 帐户自动登录到 Shmoop For Schools。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Shmoop For Schools 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Shmoop For Schools 支持 SP 发起的 SSO 
* Shmoop For Schools 支持实时  用户预配

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>从库中添加 Shmoop For Schools

要配置 Shmoop For Schools 与 Azure AD 的集成，需要从库中将 Shmoop For Schools 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Shmoop For Schools”   。
1. 从结果面板中选择“Shmoop For Schools”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>配置和测试 Shmoop For Schools 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户为 Shmoop For Schools 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Shmoop For Schools 相关用户之间建立关联。

若要为 Shmoop For Schools 配置和测试 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Shmoop For Schools SSO](#configure-shmoop-for-schools-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Shmoop For Schools 测试用户](#create-shmoop-for-schools-test-user)** - 在 Shmoop For Schools 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Shmoop For Schools”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://schools.shmoop.com/<uniqueid>` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Shmoop For Schools 客户端支持团队](mailto:support@shmoop.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Shmoop For Schools 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

    > [!NOTE]
    > Shmoop For Schools 支持两个用户角色：“教师”和“学生”   。 在 Azure AD 中设置这些角色，以便可为用户分配相应的角色。 若要了解如何在 Azure AD 中配置角色，请参阅[此文](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)。

1. 除了上述属性，Shmoop For Schools 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | Name |  源属性|
    | --------- | --------------- |
    | role      | user.assignedroles |

1. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分，我们将通过授予 B.Simon 访问 Shmoop For Schools 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Shmoop For Schools”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

    ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-shmoop-for-schools-sso"></a>配置 Shmoop For Schools SSO

若要在“Shmoop For Schools”端配置单一登录，需要将“应用联合元数据 URL”发送给 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-shmoop-for-schools-test-user"></a>创建 Shmoop For Schools 测试用户

在本部分，我们会在 Shmoop For Schools 中创建一个名为 B.Simon 的用户。 Shmoop For Schools 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Shmoop For Schools 中尚不存在用户，则会在身份验证后创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，请联系 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Shmoop For Schools”磁贴时，应当会自动登录到为其设置了 SSO 的 Shmoop For Schools。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [使用 Azure AD 试用 Shmoop For Schools](https://aad.portal.azure.com/)