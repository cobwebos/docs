---
title: 教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Symantec Web Security Service (WSS) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159945"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成

本教程介绍如何将 Symantec Web Security Service (WSS) 帐户与 Azure Active Directory (Azure AD) 帐户集成，以便 WSS 可使用 SAML 身份验证对 Azure AD 中预配的最终用户进行身份验证，并强制执行用户级或组级策略规则。

将 Symantec Web Security Service (WSS) 与 Azure AD 集成可提供以下优势：

- 从 Azure AD 门户管理 WSS 帐户使用的所有最终用户和组。

- 允许最终用户使用其 Azure AD 凭据在 WSS 中验证自己的身份。

- 对于在 WSS 帐户中定义的用户和组级策略规则启用强制执行。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Symantec Web Security Service (WSS) 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Symantec Web Security Service (WSS) 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Symantec Web Security Service (WSS) 支持 **IDP** 发起的 SSO

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>从库添加 Symantec Web Security Service (WSS)

若要配置 Symantec Web Security Service (WSS) 与 Azure AD 的集成，需将库中的 Symantec Web Security Service (WSS) 添加到托管 SaaS 应用列表。

若要从库添加 Symantec Web Security Service (WSS)，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Symantec Web Security Service (WSS)”  ，从结果面板选择“Symantec Web Security Service (WSS)”  ，然后单击“添加”  按钮添加该应用程序。

     ![结果列表中的 Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”  的测试用户配置和测试 Symantec Web Security Service (WSS) 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Symantec Web Security Service (WSS) 相关用户之间建立链接关系。

若要配置和测试 Symantec Web Security Service (WSS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **配置 Symantec Web Security Service (WSS) 单一登录** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Symantec Web Security Service (WSS) 测试用户](#create-symantec-web-security-service-wss-test-user)** - 在 Symantec Web Security Service (WSS) 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Symantec Web Security Service (WSS) 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Symantec Web Security Service (WSS) 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  对话框中，执行以下步骤：

    ![Symantec Web Security Service (WSS) 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. 在“回复 URL”文本框中键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > 如果“标识符”和“回复 URL”的值由于某种原因不起作用，请联系 [Symantec Web Security Service (WSS) 客户端支持团队](https://www.symantec.com/contact-us)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>配置 Symantec Web Security Service (WSS) 单一登录

若要在 Symantec Web Security Service (WSS) 端配置单一登录，请参阅 WSS 联机文档。 需要将下载的“联合元数据 XML”  导入到 WSS 门户。 如果配置 WSS 门户时需要帮助，请联系 [Symantec Web Security Service (WSS) 支持团队](https://www.symantec.com/contact-us)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Symantec Web Security Service (WSS) 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Symantec Web Security Service (WSS)”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Symantec Web Security Service (WSS)”  。

    ![应用程序列表中的 Symantec Web Security Service (WSS) 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-symantec-web-security-service-wss-test-user"></a>创建 Symantec Web Security Service (WSS) 测试用户

在本部分中，将在 Symantec Web Security Service (WSS) 中创建一个名为 Britta Simon 的用户。 可在 WSS 门户中手动创建相应的最终用户名，或可等待 Azure AD 中预配的用户/组在几分钟（约 15 分钟）后同步到 WSS 门户。 使用单一登录前，必须先创建并激活用户。 将用于浏览网站的最终用户计算机的公共 IP 地址也需要在 Symantec Web Security Service (WSS) 门户中进行预配。

> [!NOTE]
> 请单击[此处](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)，获取计算机的公共 IP 地址。

### <a name="test-single-sign-on"></a>测试单一登录 

由于已配置了 WSS 帐户以使用 Azure AD 进行 SAML 身份验证，在本部分中，将测试单一登录功能。

将 Web 浏览器配置为代理流向 WSS 的流量后，打开 Web 浏览器并尝试浏览到某个站点时，系统会将你重定向到 Azure 登录页面。 输入在 Azure AD 中预配的测试最终用户（即 BrittaSimon）的凭据和相关密码。 身份验证后，你将能浏览所选网站。 如果在 WSS 端创建了一条策略规则，阻止 BrittaSimon 浏览特定站点，那么尝试以 BrittaSimon 用户身份浏览该站点时，会看到 WSS 阻止页。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

