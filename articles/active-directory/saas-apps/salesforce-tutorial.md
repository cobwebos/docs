---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Salesforce 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Salesforce 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d0793c863f4f682c967c7a5ae61c5a0b78cdb4d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Salesforce 集成

本教程介绍如何将 Salesforce 与 Azure Active Directory (Azure AD) 集成。 将 Salesforce 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Salesforce。
* 让用户使用其 Azure AD 帐户自动登录到 Salesforce。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Salesforce 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Salesforce 支持 SP 发起的 SSO 

* Salesforce 支持[**自动**用户预配和取消预配](salesforce-provisioning-tutorial.md)（推荐）

* Salesforce 支持实时用户预配 

* 现在可以为 Salesforce 移动应用程序配置 Azure AD 以启用 SSO。 本教程在测试环境中配置并测试 Azure AD SSO。
* 配置 Salesforce 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>从库中添加 Salesforce

若要配置 Salesforce 与 Azure AD 的集成，需要从库中将 Salesforce 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Salesforce”   。
1. 从结果面板中选择“Salesforce”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>配置和测试 Salesforce 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户为 Salesforce 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Salesforce 相关用户之间建立关联。

若要为 Salesforce 配置和测试 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Salesforce SSO](#configure-salesforce-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Salesforce 测试用户](#create-salesforce-test-user)** - 在 Salesforce 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的 Salesforce 应用程序集成页面上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”  文本框中，使用以下模式键入值：

    企业帐户：`https://<subdomain>.my.salesforce.com`

    开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`

    b. 在“标识符”  文本框中，使用以下模式键入值：

    企业帐户：`https://<subdomain>.my.salesforce.com`

    开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)获取这些值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Salesforce”部分，根据要求复制相应的 URL  。

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

在本部分，我们将通过授予 B.Simon 访问 Salesforce 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Salesforce”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-salesforce-sso"></a>配置 Salesforce SSO

1. 要在 Salesforce 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置Salesforce”，此时会将你定向到 Salesforce 单一登录应用程序  。 在此处提供管理员凭据以登录到 Salesforce 单一登录。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-13。

    ![设置配置](common/setup-sso.png)

1. 要手动设置 Salesforce，请打开新的 Web 浏览器窗口，以管理员身份登录到 Salesforce 公司站点，并执行以下步骤：

1. 单击页面右上角设置图标  下的“安装”  。

    ![配置单一登录](./media/salesforce-tutorial/configure1.png)

1. 向下滚动到导航窗格中的“设置”  ，单击“标识”  ，以展开相关部分。 然后单击“单一登录设置”  。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso.png)

1. 在“单一登录设置”  页上，单击“编辑”  按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > 如果无法为 Salesforce 帐户启用单一登录设置，则需要联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)。

1. 选择“已启用 SAML”  ，并单击“保存”  。

    ![配置单一登录](./media/salesforce-tutorial/sf-enable-saml.png)

1. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”  。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. 单击“选择文件”  以上传从 Azure 门户下载的元数据 XML 文件，然后单击“创建”  。

    ![配置单一登录](./media/salesforce-tutorial/xmlchoose.png)

1. 在“SAML 单一登录设置”  页上，字段将自动填充，请单击“保存”。

    ![配置单一登录](./media/salesforce-tutorial/salesforcexml.png)

1. 在 Salesforce 的左侧导航窗格中，单击“公司设置”  展开相关部分，然后单击“我的域”  。

    ![配置单一登录](./media/salesforce-tutorial/sf-my-domain.png)

1. 向下滚动到“身份验证配置”  部分，并单击“编辑”  按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. 在“身份验证配置”  部分中，选中“AzureSSO”  作为 SAML SSO 配置的“身份验证服务”  ，然后单击“保存”  。

    ![配置单一登录](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 如果选择了多个身份验证服务，则当用户向 Salesforce 环境发起单一登录时，系统将提示他们选择登录时要使用的身份验证服务。 如果不希望发生这种情况，应将其他所有身份验证服务保持未选中状态  。

### <a name="create-salesforce-test-user"></a>创建 Salesforce 测试用户

在本部分，将在 Salesforce 中创建名为 B.Simon 的用户。 Salesforce 支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 时，如果 Salesforce 中没有用户，系统会创建一个新用户。 Salesforce 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Salesforce 磁贴时，应当会自动登录到为其设置了 SSO 的 Salesforce。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="test-sso-for-salesforce-mobile"></a>测试 Salesforce（移动）的 SSO

1. 打开 Salesforce 移动应用程序。 在登录页上，单击“使用自定义域”。 

    ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app1.png)

1. 在“自定义域”文本框中输入注册的自定义域名，然后单击“继续”   。

    ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app2.png)

1. 输入登录 Salesforce 应用程序所需的 Azure AD 凭据，然后单击“下一步”。 

    ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app3.png)

1. 在如下所示的“允许访问”页上单击“允许”，允许访问 Salesforce 应用程序。  

    ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app4.png)

1. 最后，在登录成功后，将显示应用程序主页。

    ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app5.png) ![Salesforce 移动应用](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](salesforce-provisioning-tutorial.md)

- [在 Azure AD 中试用 Salesforce](https://aad.portal.azure.com)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [如何通过高级可见性和控制保护 Salesforce](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
