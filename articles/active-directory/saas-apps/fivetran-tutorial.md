---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Fivetran 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Fivetran 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 9d6951456593c57f9def80990e582a5ff54cc5d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Fivetran 的集成

本教程介绍了解如何将 Fivetran 与 Azure Active Directory (Azure AD) 集成。 将 Fivetran 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Fivetran。
* 让用户使用其 Azure AD 帐户自动登录到 Fivetran。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 Fivetran 帐户。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Fivetran 支持 IDP 发起的 SSO
* Fivetran 支持实时用户预配

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-fivetran-from-the-gallery"></a>从库中添加 Fivetran

若要配置 Fivetran 与 Azure AD 的集成，需要从库中将 Fivetran 添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Fivetran” 。
1. 从结果面板中选择“Fivetran”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>配置并测试 Fivetran 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Fivetran 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Fivetran 中的相关用户之间建立链接关系。

若要配置并测试 Fivetran 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Fivetran SSO](#configure-fivetran-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Fivetran 测试用户](#create-fivetran-test-user)** - 在 Fivetran 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Fivetran”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。


1. Fivetran 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Fivetran 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Fivetran”部分，复制“登录URL”和“Azure AD 标识符”的值。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Fivetran 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Fivetran”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-fivetran-sso"></a>配置 Fivetran SSO

本部分将在“Fivetran”端配置单一登录。

1. 在另一 Web 浏览器窗口中，以帐户所有者身份登录到 Fivetran 帐户。
1. 选择窗口左上角的箭头，然后从下拉列表中选择“管理帐户”。

   ![屏幕截图，显示已选择“管理帐户”菜单选项。](media/fivetran-tutorial/fivetran-1.png)

1. 转到“设置”页的“SAML 配置”部分。

   ![显示“SAML 配置”窗格的屏幕截图，其中突出显示了配置选项。](media/fivetran-tutorial/fivetran-2.png)

   1. 对于“启用 SAML 身份验证”，请选择“打开”。
   1. 在“登录 URL”中，粘贴从 Azure 门户复制的“登录 URL”的值 。
   1. 在“证书颁发者”中，粘贴从 Azure 门户复制的“Azure AD 标识符”的值 。
   1. 在文本编辑器中打开下载的证书文件，将该证书复制到剪贴板，然后将其粘贴到“公共证书”文本框中。
   1. 选择“保存配置”。

### <a name="create-fivetran-test-user"></a>创建 Fivetran 测试用户

在本部分，将在 Fivetran 中创建一个名为 B.Simon 的用户。 Fivetran 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Fivetran 中尚不存在用户，将在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Fivetran 

2. 可以使用 Microsoft 访问面板。 在访问面板中单击“Fivetran”磁贴时，应会自动登录到为其设置了 SSO 的 Fivetran。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Fivetran 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

