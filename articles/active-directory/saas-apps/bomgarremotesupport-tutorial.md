---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 BeyondTrust Remote Support 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BeyondTrust Remote Support 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082063"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>教程：Azure Active Directory 单一登录 (SSO) 与 BeyondTrust Remote Support 集成

本教程介绍如何将 BeyondTrust Remote Support 与 Azure Active Directory (Azure AD) 集成。 将 BeyondTrust Remote Support 与 Azure AD 集成时，你可以：

* 控制 Azure AD 中有权访问 BeyondTrust Remote Support 的人员。
* 让用户使用其 Azure AD 帐户自动登录到 BeyondTrust Remote Support。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 BeyondTrust Remote Support 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* BeyondTrust Remote Support 支持 SP 发起的 SSO 
* BeyondTrust Remote Support 支持实时用户预配 

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>从库中添加 BeyondTrust Remote Support

要配置 BeyondTrust Remote Support 与 Azure AD 的集成，需要从库中将 BeyondTrust Remote Support 添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“BeyondTrust Remote Support”   。
1. 从结果面板中选择“BeyondTrust Remote Support”，然后添加应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>配置和测试 BeyondTrust Remote Support 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 BeyondTrust Remote Support 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 BeyondTrust Remote Support 中的相关用户之间建立链接关系。

若要配置和测试 BeyondTrust Remote Support 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 BeyondTrust Remote Support SSO](#configure-beyondtrust-remote-support-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 BeyondTrust Remote Support 测试用户](#create-beyondtrust-remote-support-test-user)** - 在 BeyondTrust Remote Support 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“BeyondTrust Remote Support”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<HOSTNAME>.bomgar.com/saml`。

    b. 在“标识符”框中，使用以下模式键入 URL：`https://<HOSTNAME>.bomgar.com` 

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 本教程后面的步骤中将介绍这些值。

1. BeyondTrust Remote Support 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，BeyondTrust Remote Support 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | Name |  源属性|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | Name | user.userprincipalname |
    | 用户名 | user.userprincipalname |
    | 组 | user.groups |
    | 唯一用户标识符 | user.userprincipalname |

    > [!NOTE]
    > 为 BeyondTrust Remote Support 应用程序分配 Azure AD 组时，需将“声明中返回的组”选项从 None 修改为 SecurityGroup。 这些组将作为其对象 ID 导入到应用程序中。 通过检查 Azure Active Directory 接口中的属性来查找 Azure AD 组的对象 ID。 将需要执行该操作以引用 Azure AD 组并将其分配至正确的组策略。

1. 设置唯一用户标识符时，必须将此值设置为 NameID 格式：永久  。 我们要求选用永久标识符，以便正确标识用户并将其关联到正确的组策略中以获得权限。 单击编辑图标以打开“用户属性和声明”对话框，并编辑唯一用户标识符值  。

1. 在“管理声明”部分，单击“选择名称标识符格式”，并将值设置为“永久”，然后单击“保存”     。

    ![用户特性和声明](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 BeyondTrust Remote Support”部分中，根据要求复制相应的 URL  。

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

在本部分，我们通过授予 B.Simon 访问 BeyondTrust Remote Support 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“BeyondTrust Remote Support”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-beyondtrust-remote-support-sso"></a>配置 BeyondTrust Remote Support SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 BeyondTrust Remote Support。

1. 单击“状态”菜单并复制“标识符”、“回复 URL”和“登录 URL”，然后在 Azure 门户中的“基本 SAML 配置”部分使用这些值      。

    ![配置 BeyondTrust Remote Support](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. 在 `https://support.example.com/login` 导航到 BeyondTrust Remote Support/登录接口，其中“support.example.com”是设备的主要主机名，并使用管理凭据进行身份验证  。

1. 导航到“用户和安全” > “安全提供程序”   。

1. 在下拉菜单中选择“SAML”并单击“创建提供程序”按钮   。

1. 在“标识提供者设置”部分下，有一个用于上传“标识提供者元数据”的选项。 找到从 Azure 门户下载的元数据 XML 文件，然后单击“上传”按钮  。 将自动上传“实体 ID”、“单一登录服务 URL”和证书，并需要将“协议绑定”更改为“HTTP POST”     。 参阅以下屏幕截图：

    ![配置 BeyondTrust Remote Support](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>创建 BeyondTrust Remote Support 测试用户

我们将在此处配置用户预配设置。 本部分中使用的值将从 Azure 门户的“用户属性和声明”部分中引用  。 我们将其配置为创建时已导入的默认值，但是，可以根据需要自定义该值。

![正在创建用户](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> 该实现不需要组和电子邮件属性。 如果使用 Azure AD 组并将其分配给 BeyondTrust Remote Support 组策略以获取权限，则需要通过 Azure 门户中的属性来引用该组的对象 ID，并将其置于“可用组”部分中。 完成此任务后，可将对象 ID/AD 组分配给组策略以获得权限。

![正在创建用户](./media/bomgarremotesupport-tutorial/config-user2.png)

![正在创建用户](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> 或者，可以在 SAML2 安全提供程序上设置默认的组策略。 通过定义此选项，这将为通过 SAML 进行身份验证的所有用户分配组策略中指定的权限。 “常规成员”策略包含在具有有限权限的 BeyondTrust Remote Support/Privileged Remote Access 中，可用于测试身份验证并将用户分配给正确的策略。 在首次成功进行身份验证尝试之前，用户不会通过“登录”>“用户和安全性”填充到 SAML2 用户列表中。 有关组策略的其他信息，请查看以下链接：`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“BeyondTrust Remote Support”磁贴时，应会自动登录到设置了 SSO 的 BeyondTrust Remote Support。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [使用 Azure AD 试用 BeyondTrust Remote Support](https://aad.portal.azure.com/)
