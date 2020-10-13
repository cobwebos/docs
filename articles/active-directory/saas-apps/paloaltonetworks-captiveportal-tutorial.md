---
title: 教程：Azure Active Directory 与 Palo Alto Networks Captive Portal 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks Captive Portal 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1096437fc1d77042a9db4dc359d51cd6d9d22960
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304383"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>教程：Azure Active Directory 与 Palo Alto Networks Captive Portal 的集成

本教程介绍如何将 Palo Alto Networks Captive Portal 与 Azure Active Directory (Azure AD) 集成。
将 Palo Alto Networks Captive Portal 与 Azure AD 集成有以下好处：

* 可在 Azure AD 中控制谁有权访问 Palo Alto Networks Captive Portal。
* 可以让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks Captive Portal（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks Captive Portal 的集成，需要准备好以下各项：

* 一个 Azure Active Directory 订阅。 如果没有 Azure AD，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个启用了 Palo Alto Networks Captive Portal 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Palo Alto Networks Captive Portal 支持 IDP 发起的 SSO
* Palo Alto Networks Captive Portal 支持实时用户预配

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>从库中添加 Palo Alto Networks Captive Portal

若要配置 Palo Alto Networks Captive Portal 与 Azure AD 的集成，需要从库中将 Palo Alto Networks Captive Portal 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Palo Alto Networks Captive Portal”。 
1. 在结果面板中选择“Palo Alto Networks Captive Portal”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

在本部分中，我们将基于名为“B.Simon”的测试用户配置和测试 Palo Alto Networks Captive Portal 的 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 Palo Alto Networks Captive Portal 相关用户之间建立关联。

若要为 Palo Alto Networks Captive Portal 配置和测试 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用用户 B.Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 将 B.Simon 设置为使用 Azure AD 单一登录。
2. **[配置 Palo Alto Networks Captive Portal SSO](#configure-palo-alto-networks-captive-portal-sso)** - 在应用程序中配置单一登录设置。
    * **[创建 Palo Alto Networks Captive Portal 测试用户](#create-a-palo-alto-networks-captive-portal-test-user)** - 在 Palo Alto Networks Captive Portal 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Palo Alto Networks Captive Portal”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”窗格中，执行以下步骤：

   1. 对于“标识符”，请输入采用 `https://<customer_firewall_host_name>/SAML20/SP` 模式的 URL。 

   2. 对于“回复 URL”，请输入采用 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 模式的 URL。 

      > [!NOTE]
      > 请将此步骤中的占位符值更新为实际的标识符和回复 URL。 若要获取实际值，请联系 [Palo Alto Networks Captive Portal 客户端支持团队](https://support.paloaltonetworks.com/support)。

5. 在“SAML 签名证书”部分中的“联合元数据 XML”旁边，选择“下载”。    将下载的文件保存在计算机上。

    ![联合元数据 XML 下载链接](common/metadataxml.png)

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

在本部分中，我们通过授予 B.Simon 访问 Palo Alto Networks Captive Portal 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Palo Alto Networks Captive Portal”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>配置 Palo Alto Networks Captive Portal SSO

接下来，在 Palo Alto Networks Captive Portal 中设置单一登录：

1. 在另一个浏览器窗口中，以管理员身份登录到 Palo Alto Networks 网站。

2. 选择“设备”选项卡。 

    ![Palo Alto Networks 网站上的“设备”选项卡](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在菜单中，依次选择“SAML 标识提供者”、“导入”。  

    ![“导入”按钮](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在“SAML 标识提供者服务器配置文件导入”对话框中完成以下步骤： 

    ![配置 Palo Alto 网络单一登录](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. 对于“配置文件名称”，请输入类似于 **AzureAD-CaptivePortal** 的名称。 
    
    2. 在“标识提供者元数据”旁边，选择“浏览”。   选择在 Azure 门户中下载的 metadata.xml 文件。
    
    3. 选择“确定”  。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>创建 Palo Alto Networks Captive Portal 测试用户

接下来，在 Palo Alto Networks Captive Portal 中创建名为 *Britta Simon* 的用户。 Palo Alto Networks Captive Portal 支持默认已启用的实时用户预配功能。 在本部分无需完成任何任务。 如果 Palo Alto Networks Captive Portal 中不存在任何用户，身份验证后会创建一个新用户。

> [!NOTE]
> 若要手动创建用户，请联系 [Palo Alto Networks Captive Portal 客户端支持团队](https://support.paloaltonetworks.com/support)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Palo Alto Networks Captive Portal

可以使用 Microsoft 访问面板。 单击访问面板中的 Palo Alto Networks Captive Portal 磁贴时，应当会自动登录到为其设置了 SSO 的 Palo Alto Networks Captive Portal。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Palo Alto Networks Captive Portal 后，就可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
