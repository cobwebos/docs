---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 F5 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 F5 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>教程：Azure Active Directory 单一登录 (SSO) 与 F5 集成

本教程介绍如何将 F5 与 Azure Active Directory (Azure AD) 集成。 将 F5 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 F5。
* 让用户使用其 Azure AD 帐户自动登录到 F5。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 F5 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* 可通过三种不同的方式配置 F5 SSO。

- [为 Header Based 应用程序配置 F5 单一登录](#configure-f5-single-sign-on-for-header-based-application)

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为高级 Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>从库中添加 F5

若要配置 F5 与 Azure AD 的集成，需要从库中将 F5 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“F5”   。
1. 从结果面板中选择“F5”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>配置和测试 F5 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 F5 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 F5 相关用户之间建立链接关系。

若要配置和测试 F5 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 F5 SSO](#configure-f5-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 F5 测试用户](#create-f5-test-user)** - 在 F5 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“F5”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [F5 客户端支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 F5”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 F5 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“F5”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-f5-sso"></a>配置 F5 SSO

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为高级 Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>为 Header Based 应用程序配置 F5 单一登录

1. 打开新的 Web 浏览器窗口，以管理员身份登录 F5 (Header Based) 公司站点，并执行以下步骤：

1. 需要将元数据证书导入到 F5 (Header Based) 中，该证书将在稍后的设置过程中使用。 转到“系统”>“证书管理”>“流量证书管理”>>“SSL 证书列表”  。 单击右下角的“导入” 

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure12.png)
 
1. 此外，还需要将 **SSL 证书**作为主机名 (`headerapp.superdemo.live`)，在本例中我们使用了通配符证书。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure13.png)

1. 转到 - “F5 (Header Based) BIG-IP”，单击“访问权限”>“引导式配置”>“联合身份验证”>“SAML 服务提供程序”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure01.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure02.png)
 
1. 指定**实体 ID**（与你在“Azure AD 应用程序配置”上配置的相同）

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure03.png) 

1. 创建新的虚拟服务器，指定**目标地址**，**重定向端口**是可选项。 选择我们之前上传的**通配符证书**（或你为应用程序上传的**证书**）和**关联私钥**。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure04.png) 

1. 上传配置**元数据**并指定新的 **SAML IDP 连接器名称**，还需要指定先前上传的联合身份验证证书。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure05.png)
 
1. **新建**后端应用池，指定后端应用程序服务器的 **IP 地址**。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure06.png)

1. 在“单一登录”下，选择“基于 HTTP 标头”  。 可以根据应用程序添加其他标头。 有关 SAML 会话变量的列表，请参阅附录

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure07.png) 

1. 请联系 [F5 (Header Based) 客户端支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)以获取**终结点检查属性**文档详细信息。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure08.png)

1. 请联系 [F5 (Header Based) 客户端支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)以获取**会话管理属性**文档详细信息。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure09.png)

1. **查看摘要**并单击“部署”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure10.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>创建 F5 测试用户

在本部分中，将在 F5 中创建名为 B.Simon 的用户。 请与  [F5 客户端支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)协作，将用户添加到 F5 平台中。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 F5 磁贴时，应当会自动登录到设置了 SSO 的 F5。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 F5](https://aad.portal.azure.com/)

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为高级 Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

