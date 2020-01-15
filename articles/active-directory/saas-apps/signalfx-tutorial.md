---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SignalFx 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SignalFx 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443284"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SignalFx 集成

本教程介绍如何将 SignalFx 与 Azure Active Directory (Azure AD) 进行集成。 将 SignalFx 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SignalFx。
* 让用户使用其 Azure AD 帐户自动登录到 SignalFx。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SignalFx 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SignalFx 支持 **IDP** 发起的 SSO
* SignalFx 支持**实时**用户预配

## <a name="adding-signalfx-from-the-gallery"></a>从库中添加 SignalFx

若要配置 SignalFx 与 Azure AD 的集成，需要从库中将 SignalFx 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SignalFx”   。
1. 从结果面板中选择“SignalFx”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>配置和测试 SignalFx 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 SignalFx 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 SignalFx 相关用户之间建立关联关系。

若要配置和测试 SignalFx 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SignalFx SSO](#configure-signalfx-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 SignalFx 测试用户](#create-signalfx-test-user)** - 在 SignalFx 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“SignalFx”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”文本框中键入 URL：`https://api.signalfx.com/v1/saml/metadata` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > 上面的值不是实际值。 需要使用实际的回复 URL 更新该值（本教程稍后将会介绍）。

1. SignalFx 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，SignalFx 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 SignalFx”部分，根据要求复制相应的 URL。 

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

在本部分中，将通过授予 B.Simon 访问 SignalFx 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SignalFx”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-signalfx-sso"></a>配置 SignalFx SSO

1. 以管理员身份登录到 SignalFx 公司站点。

1. 在 SignalFx 中，在顶部单击“集成”  以打开“集成”页。

    ![SignalFx 集成](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. 在“登录服务”部分下单击 **Azure Active Directory** 磁贴。 

    ![SignalFx SAML](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. 单击“新建集成”  并在“安装”  选项卡下执行以下步骤：

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. 在“名称”  文本框中，键入一个新的集成名称，例如 **OurOrgName SAML SSO**。

    b. 复制“集成 ID”值，并将其追加到“回复 URL”，取代 Azure 门户中“基本 SAML 配置”部分的“回复 URL”文本中的 `<integration ID>`。    

    c. 在“证书”文本框中，单击“上传文件”  以上传从 Azure 门户下载的 **Base64 编码证书**。 

    d. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    e. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。

    f. 单击“ **保存**”。

### <a name="create-signalfx-test-user"></a>创建 SignalFx 测试用户

本部分的目的是在 SignalFx 中创建名为 Britta Simon 的用户。 SignalFx 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 SignalFx 期间，如果尚不存在用户，则会创建一个新用户。

当用户首次通过 SAML SSO 登录到 SignalFx 时，[SignalFx 支持团队](mailto:kmazzola@signalfx.com)会向其发送一封电子邮件，其中包含他们必须单击以进行身份验证的链接。 这仅在用户首次登录时发生，后续登录尝试不要求进行电子邮件验证。

> [!Note]
> 如果需要手动创建用户，请联系 [SignalFx 支持团队](mailto:kmazzola@signalfx.com)

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SignalFx”磁贴时，应会自动登录到设置了 SSO 的 SignalFx。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试结合使用 SignalFx 和 Azure AD](https://aad.portal.azure.com/)