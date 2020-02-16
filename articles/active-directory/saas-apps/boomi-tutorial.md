---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Boomi 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Boomi 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086453"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Boomi 的集成

本教程介绍如何将 Boomi 与 Azure Active Directory (Azure AD) 集成。 将 Boomi 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Boomi。
* 让用户使用其 Azure AD 帐户自动登录到 Boomi。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Boomi 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Boomi 支持 **IDP** 发起的 SSO
* 配置 Boomi 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-boomi-from-the-gallery"></a>从库中添加 Boomi

要配置 Boomi 与 Azure AD 的集成，需要从库中将 Boomi 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Boomi”   。
1. 从结果面板中选择“Boomi”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>配置和测试 Boomi 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Boomi 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Boomi 中的相关用户之间建立链接关系。

若要配置并测试 Boomi 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Boomi SSO](#configure-boomi-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Boomi 测试用户](#create-boomi-test-user)** - 在 Boomi 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Boomi”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
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
    > 你将从“配置 Boomi SSO”  部分获得**服务提供商元数据文件**，本教程稍后将对此进行说明。 如果“标识符”和“回复 URL”值未自动填充，请根据要求手动填充这些值。  

1. Boomi 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Boomi 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Boomi”部分中，根据要求复制相应的 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 Boomi 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Boomi”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-boomi-sso"></a>配置 Boomi SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Boomi 公司站点。

1. 导航到“公司名称”  ，并转到“设置”  。

1. 单击“SSO 选项”  选项卡，并执行以下步骤。

    ![在应用端配置单一登录](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. 选中“启用 SAML 单一登录”  复选框。

    b. 单击“导入”  ，将已下载的证书从 Azure AD 上传到“标识提供者证书”  。

    c. 在“标识提供者登录 URL”  文本框中，放置 Azure AD 应用程序配置窗口中**登录 URL** 的值。

    d. 对于“联合 ID 位置”，选择“联合 ID 位于 FEDERATION_ID 属性元素中”单选按钮。  

    e. 复制 **AtomSphere 元数据 URL**，通过所选浏览器转到**元数据 URL**，并将输出保存到文件。 在 Azure 门户的“基本 SAML 配置”  部分中上传**元数据 URL**。

    f. 单击“保存”按钮  。

### <a name="create-boomi-test-user"></a>创建 Boomi 测试用户

为使 Azure AD 用户能够登录到 Boomi，必须将其预配到 Boomi 中。 就 Boomi 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录 Boomi 公司站点。

1. 登录后，导航到“用户管理”  并转到“用户”  。

    ![用户](./media/boomi-tutorial/tutorial_boomi_001.png "用户")

1. 单击 **+** 图标，此时会打开“添加/维护用户角色”  对话框。

    ![用户](./media/boomi-tutorial/tutorial_boomi_002.png "用户")

    ![用户](./media/boomi-tutorial/tutorial_boomi_003.png "用户")

    a. 在“用户电子邮件地址”文本框中  ，键入用户的电子邮件地址（例如 B.Simon@contoso.com）。

    b. 在“名字”文本框中，键入用户的名字（如“B.”）  。

    c. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）  。

    d. 输入用户的**联合 ID**。 每个用户都必须具有在帐户中唯一标识用户的联合 ID。

    e. 向用户分配“标准用户”  角色。 不要分配管理员角色，因为这会向用户提供标准 Atmosphere 访问权限以及单一登录访问权限。

    f. 单击“确定”。 

    > [!NOTE]
    > 用户不会收到包含可用于登录 AtomSphere 帐户的密码的欢迎通知电子邮件，因为其密码是通过标识提供者进行管理的。 可以使用任何其他 Boomi 用户帐户创建工具或 Boomi 提供的 API 来预配 AAD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Boomi 磁贴时，应当会自动登录到已为其设置了 SSO 的 Boomi。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [通过 Azure AD 试用 Boomi](https://aad.portal.azure.com/)
