---
title: 教程：Azure Active Directory 与 HubSpot 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 HubSpot 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100895"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>教程：Azure Active Directory 与 HubSpot 的集成

在本教程中，了解如何将 HubSpot 与 Azure Active Directory (Azure AD) 集成。

将 HubSpot 与 Azure AD 集成提供以下优势：

* 可使用 Azure AD 控制谁有权访问 HubSpot。
* 用户可使用其 Azure AD 帐户自动登录到 HubSpot（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 HubSpot 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 HubSpot 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置和测试 Azure AD 单一登录，并将 HubSpot 与 Azure AD 集成。

HubSpot 支持以下功能：

* **SP 发起的单一登录**
* **IDP 发起的单一登录**

## <a name="add-hubspot-in-the-azure-portal"></a>在 Azure 门户中添加 HubSpot

若要将 HubSpot 与 Azure AD 集成，必须将 HubSpot 添加到托管 SaaS 应用列表中。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加应用程序，请选择“新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“HubSpot”  。 在搜索结果中选择“HubSpot”，然后选择“添加”   。

    ![结果列表中的 HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 HubSpot 的 Azure AD 单一登录。 若要运行单一登录，必须在 Azure AD 用户与 HubSpot 相关用户之间建立链接关系。

若要配置和测试 HubSpot 的 Azure AD 单一登录，必须完成以下构建基块：

| 任务 | 说明 |
| --- | --- |
| **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** | 使用户能够使用此功能。 |
| **[配置 HubSpot 单一登录](#configure-hubspot-single-sign-on)** | 在应用程序中配置单一登录设置。 |
| **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** | 测试名为 Britta Simon 的用户的 Azure AD 单一登录。 |
| **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** | 使 Britta Simon 能够使用 Azure AD 单一登录。 |
| **[创建 HubSpot 测试用户](#create-a-hubspot-test-user)** | 在 HubSpot 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。 |
| **[测试单一登录](#test-single-sign-on)** | 验证配置是否正常工作。 |

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中配置 HubSpot 的 Azure AD 单一登录。

1. 在 [Azure 门户](https://portal.azure.com/)的“HubSpot”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML”或“SAML/WS-Fed”模式以启用单一登录    。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）以打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”窗格中，若要配置“IDP 发起模式”，请完成以下步骤：  

    1. 在“标识符”框中，输入具有以下模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>  。

    1. 在“回复 URL”框中，输入具有以下模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>  。

    ![HubSpot 域和 URL 单一登录信息](common/idp-intiated.png)

    > [!NOTE]
    > 若要设置 URL 的格式，还可参考 Azure 门户的“基本 SAML 配置”窗格中显示的模式  。

1. 若要在 *SP 发起*模式下配置应用程序，请执行以下操作：

    1. 选择“设置其他 URL”  。

    1. 在“登录 URL”框中，输入“https:\//app.hubspot.com/login”   。

    ![“设置其他 URL”选项](common/metadata-upload-additional-signon.png)

1. 在“设置 SAML 单一登录”窗格的“SAML 签名证书”部分中，选择“证书 (Base64)”旁边的“下载”     。 根据需要选择下载选项。 将证书保存在计算机上。

    ![证书 (Base64) 下载选项](common/certificatebase64.png)

1. 在“设置 HubSpot”部分，根据要求复制以下 URL  ：

    * 登录 URL
    * Azure AD 标识符
    * 注销 URL

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>配置 HubSpot 单一登录

1. 在浏览器中打开新选项卡并登录到 HubSpot 管理员帐户。

1. 选择页面右上角的**设置**图标。

    ![HubSpot 中的​​设置图标](./media/hubspot-tutorial/config1.png)

1. 选择“帐户默认值”  。

    ![HubSpot 中的“​​帐户默认值”选项](./media/hubspot-tutorial/config2.png)

1. 向下滚动到“安全”部分，然后选择“设置”   。

    ![HubSpot 中的“​​设置”选项](./media/hubspot-tutorial/config3.png)

1. 在“设置单一登录”部分中，完成以下步骤  ：

    1. 在“受众 URl (服务提供程序实体 ID)”框中，选择“复制”以复制值   。 在 Azure 门户的“基本 SAML 配置”窗格中，将值粘贴到“标识符”框中   。

    1. 在“登录 URl、ACS、收件人或重定向”框中，选择“复制”以复制值   。 在 Azure 门户的“基本 SAML 配置”窗格中，将值粘贴到“回复 URL”框中   。

    1. 在 HubSpot 的“标识提供者或证书颁发者 URL”框中，粘贴在 Azure 门户中复制的“Azure AD 标识符”值   。

    1. 在 HubSpot 的“标识提供者单一登录 URL”框中，粘贴在 Azure 门户中复制的“登录 URL”值   。

    1. 在 Windows 记事本中，打开下载的“证书(Base64)”文件。 选择并复制文件的内容。 然后在 HubSpot 中将其粘贴到“X.509 证书”框中 **** 。

    1. 选择“验证”  。

        ![HubSpot 中的“设置单一登录”部分](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 选择“新建用户”。 

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中完成以下步骤： 

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入“brittasimon\@\<your-company-domain>.\<extension\>”   。 例如，brittasimon\@contoso.com  。

    1. 选中“显示密码”复选框  。 记下“密码”框中显示的值  。

    1. 选择“创建”  。

    ![“用户”窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 HubSpot 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “HubSpot”    。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在应用程序列表中，选择“HubSpot”  。

    ![应用程序列表中的 HubSpot](common/all-applications.png)

1. 在菜单中选择“用户和组”  。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”窗格中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，在用户列表中选择“Britta Simon”   。 选择“选择”。 

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相关的角色  。 选择“选择”。 

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-a-hubspot-test-user"></a>创建 HubSpot 测试用户

若要使 Azure AD 用户能够登录到 HubSpot，必须在 HubSpot 中预配该用户。 在 HubSpot 中，预配是一项手动任务。

若要在 HubSpot 中预配用户帐户，请执行以下操作：

1. 以管理员身份登录 HubSpot 公司站点。

1. 选择页面右上角的**设置**图标。

    ![HubSpot 中的​​设置图标](./media/hubspot-tutorial/config1.png)

1. 选择“用户和团队”  。

    ![HubSpot 中的“用户和团队”选项](./media/hubspot-tutorial/user1.png)

1. 选择“创建用户”  。

    ![HubSpot 中的“创建用户”选项](./media/hubspot-tutorial/user2.png)

1. 在“添加电子邮件地址”框中，按 brittasimon\@contoso.com 的格式输入用户的电子邮件地址，然后选择“下一步”   。

    ![HubSpot 中“创建用户”部分的“添加电子邮件地址”框](./media/hubspot-tutorial/user3.png)

1. 在“创建用户”部分中，选择每个选项卡  。在每个选项卡上，为用户设置相关选项和权限。 然后，选择“下一步”  。

    ![HubSpot 中“创建用户”部分的各个选项卡](./media/hubspot-tutorial/user4.png)

1. 若要向用户发送邀请，请选择“发送”  。

    ![HubSpot 中的“发送”选项](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 用户接受邀请后，将激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

设置单一登录后，如果在“我的应用”门户中选择“HubSpot”，将自动登录到 HubSpot  。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [用于将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
