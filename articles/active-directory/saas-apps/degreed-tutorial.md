---
title: 教程：Azure Active Directory 与 Degreed 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Degreed 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: b0cc937cae4230d3310b682a51988243f0207fc0
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054154"
---
# <a name="tutorial-azure-active-directory-integration-with-degreed"></a>教程：Azure Active Directory 与 Degreed 集成

本教程介绍如何将 Degreed 与 Azure Active Directory (Azure AD) 集成。
将 Degreed 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Degreed。
* 可让用户使用其 Azure AD 帐户自动登录到 Degreed（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Degreed 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Degreed 单一登录的订阅

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Degreed 支持 **SP** 发起的 SSO

* Degreed 支持**恰时**用户预配

* 配置 Degreed 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-degreed-from-the-gallery"></a>从库中添加 Degreed

要配置 Degreed 与 Azure AD 的集成，需要从库中将 Degreed 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Degreed” 。
1. 从结果面板中选择“Degreed”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Degreed 的 Azure AD 单一登录。****
若要运行单一登录，需要在 Azure AD 用户与 Degreed 相关用户之间建立链接关系。

若要配置并测试 Degreed 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 Degreed SSO](#configure-degreed-sso)** - 在应用程序端配置单一登录设置。
    * [创建 Degreed 测试用户](#create-degreed-test-user) - 在 Degreed 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式****。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的 Degreed 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Degreed 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://degreed.com/?orgsso=<company code>` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://degreed.com/<instancename>`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://degreed.com/SAML/<instancename>`
    
    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Degreed 客户端支持团队](mailto:admin@degreed.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Degreed”部分，根据要求复制相应 URL****。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。  
1. 在屏幕顶部选择“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“姓名”字段中输入 **B.Simon**。  
   1. 在“用户名”字段中输入 `<username>@<companydomain>.<extension>`。 例如：`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值 。
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将授予 B.Simon 访问 Degreed 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Degreed”。****
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="configure-degreed-sso"></a>配置 Degreed SSO

若要在 **Degreed** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Degreed 支持团队](mailto:admin@degreed.com)。**** 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-degreed-test-user"></a>创建 Degreed 测试用户

本部分的目的是在 Degreed 中创建名为 Britta Simon 的用户。 Degreed 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 如果尚不存在用户，则在尝试访问 Degreed 期间创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [Degreed 支持团队](mailto:admin@degreed.com)。


## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Degreed 磁贴时，会自动登录到为其设置了 SSO 的 Degreed。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

