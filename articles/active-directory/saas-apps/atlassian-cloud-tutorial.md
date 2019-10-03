---
title: 教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106593"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>教程：将 Atlassian Cloud 与 Azure Active Directory 集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。 将 Atlassian Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权限访问 Atlassian Cloud。
* 让用户使用其 Azure AD 帐户自动登录到 Atlassian Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Atlassian Cloud 单一登录 (SSO) 的订阅。
* 若要对 Atlassian Cloud 产品启用安全断言标记语言 (SAML) 单一登录，需要设置 Atlassian Access。 详细了解 [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Atlassian Cloud 支持 SP 和 IDP 发起的 SSO 

## <a name="adding-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud

要配置 Atlassian Cloud 与 Azure AD 的集成，需要从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Atlassian Cloud”   。
1. 从结果面板中选择“Atlassian Cloud”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Atlassian Cloud 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Atlassian Cloud 相关用户之间建立链接关系。

若要配置和测试 Atlassian Cloud 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Atlassian Cloud SSO](#configure-atlassian-cloud-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B.Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B.Simon 能够使用 Azure AD 单一登录。
5. **[创建 Atlassian Cloud 测试用户](#create-atlassian-cloud-test-user)** - 在 Atlassian Cloud 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Atlassian Cloud”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://auth.atlassian.com/saml/<unique ID>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. 单击“设置其他 URL”  。

    d. 在“中继状态”文本框中，使用以下模式键入 URL：`https://<instancename>.atlassian.net` 

    > [!NOTE]
    > 上面的值不是实际值。 请使用实际标识符和回复 URL 更新这些值。 可以  从“Atlassian Cloud SAML 配置”屏幕获取这些实际值，具体将在本教程的“配置 Atlassian Cloud 单一登录”部分  中进行介绍。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<instancename>.atlassian.net`

    > [!NOTE]
    > “登录 URL”值不是实际值。 粘贴用于登录到 Atlassian Cloud 管理门户的实例中的值。

    ![配置单一登录](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Atlassian Cloud 应用程序需要特定格式的 SAML 断言，这需要向“SAML 令牌属性”配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Atlassian Cloud 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。 

    ![image](common/edit-attribute.png)

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Atlassian Cloud”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-atlassian-cloud-sso"></a>配置 Atlassian Cloud SSO

1. 若要在 Atlassian Cloud 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”  ，方法是单击“安装扩展”  。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Atlassian Cloud”  会将你定向到 Atlassian Cloud 应用程序。 在此处，提供管理员凭据以登录到 Atlassian Cloud。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 如果想要手动设置 Atlassian Cloud，请打开新的 Web 浏览器窗口并以管理员身份登录到 Atlassian Cloud 公司网站，并执行以下步骤：

4. 在打算配置单一登录前，需要对你的域进行验证。 有关详细信息，请参阅 [Atlassian 域验证](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)文档。

5. 在左侧窗格中，选择“安全性”   > “SAML 单一登录”  。 如果尚未订阅 Atlassian Identity Manager，请订阅。

    ![配置单一登录](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. 在“添加 SAML 配置”窗口中执行以下操作： 

    ![配置单一登录](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. 在“标识提供者实体 ID”框中，粘贴从 Azure 门户复制的 **Azure AD 标识符**  。

    b. 在“标识提供者 SSO URL”框中，粘贴从 Azure 门户复制的**登录 URL**  。

    c. 在 Azure 门户中打开下载的证书 .txt 文件，复制其中包含的值（不要复制 *Begin Certificate* 和 *End Certificate* 行），然后将其粘贴到“公共 X509 证书”框中。 

    d. 单击“保存配置”  。

7. 为了确保设置正确的 URL，请执行以下操作来更新 Azure AD 设置：

    ![配置单一登录](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. 在“SAML”窗口中，复制“SP 标识 ID”，然后在 Azure 门户中 Atlassian Cloud 的“基本 SAML 配置”下，将此 ID 粘贴到“标识符”框中。   

    b. 在“SAML”窗口中，复制“SP 断言使用者服务 URL”，然后在 Azure 门户中 Atlassian Cloud 的“基本 SAML 配置”下，将此 URL 粘贴到“回复 URL”框中。    “登录 URL”是 Atlassian Cloud 的租户 URL。

    > [!NOTE]
    > 现有客户在 Azure 门户中更新“SP 标识 ID”和“SP 断言使用者服务 URL”值之后，应选择“是，更新配置”。    新客户可以跳过此步骤。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Atlassian Cloud 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Atlassian Cloud”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择“用户”列表中的“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

若要使 Azure AD 用户能够登录到 Atlassian Cloud，请执行以下操作，在 Atlassian Cloud 中手动预配用户帐户：

1. 在“管理”窗格中，选择“用户”。  

    ![Atlassian Cloud“用户”链接](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. 若要在 Atlassian Cloud 中创建用户，请选择“邀请用户”。 

    ![创建 Atlassian Cloud 用户](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. 在“电子邮件地址”框中输入该用户的电子邮件地址，然后分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. 若要向该用户发送电子邮件邀请，请选择“邀请用户”。  随后会将一封电子邮件邀请发送给该用户，接受邀请后，该用户将在系统中激活。

> [!NOTE]
> 还可以通过选择“用户”部分中的“批量创建”按钮来批量创建用户。  

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 Atlassian Cloud 磁贴时，应当会自动登录到设置了 SSO 的 Atlassian Cloud。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)