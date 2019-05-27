---
title: 教程：Azure Active Directory 与 Palo Alto Networks Captive Portal 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks Captive Portal 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870013"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>教程：Azure Active Directory 与 Palo Alto Networks Captive Portal 的集成

本教程介绍如何将 Palo Alto Networks Captive Portal 与 Azure Active Directory (Azure AD) 集成。

将 Palo Alto Networks Captive Portal 与 Azure AD 集成可获得以下优势：

* 在 Azure AD 中可以控制谁有权访问 Palo Alto Networks Captive Portal。
* 可让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks Captive Portal（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks Captive Portal 的集成，需要准备好以下各项：

* 一个 Azure Active Directory 订阅。 如果没有 Azure AD，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个启用了 Palo Alto Networks Captive Portal 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

Palo Alto Networks Captive Portal 支持以下方案：

* **IDP 发起的单一登录**
* **实时用户预配**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>从库中添加 Palo Alto Networks Captive Portal

若要开始，请将库中的 Palo Alto Networks Captive Portal 添加到托管 SaaS 应用列表：

1. 在 [Azure 门户](https://portal.azure.com) 的左侧菜单中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 选择“企业应用程序” > “所有应用程序”。

    ![菜单中的“企业应用程序”选项](common/enterprise-applications.png)

3. 选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入 **Palo Alto Networks Captive Portal**。 在搜索结果中选择“Palo Alto Networks Captive Portal”，然后选择“添加”。

     ![结果列表中的 Palo Alto Networks - 强制网络门户](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

基于名为 *Britta Simon* 的测试用户配置并测试 Palo Alto Networks Captive Portal 的 Azure AD 单一登录。 若要正常运行单一登录，必须在 Azure AD 用户与 Palo Alto Networks Captive Portal 中的相同用户之间建立关系。 

若要配置并测试 Palo Alto Networks Captive Portal 的 Azure AD 单一登录，请完成以下任务：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)**：使用户能够使用此功能。
2. **[配置 Palo Alto Networks Captive Portal 单一登录](#configure-palo-alto-networks-captive-portal-single-sign-on)**：在应用程序中配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)**：使用用户 *Britta Simon* 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)**：将 Britta Simon 设置为使用 Azure AD 单一登录。
5. **创建 Palo Alto Networks Captive Portal 测试用户**：在 Palo Alto Networks Captive Portal 中创建一个链接到 Azure AD 用户的对应用户 *Britta Simon*。
6. **[测试单一登录](#test-single-sign-on)**：验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

首先，在 Azure 门户中启用 Azure AD 单一登录：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Palo Alto Networks - 强制网络门户**应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”窗格中选择“SAML”。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”窗格中，选择“编辑”铅笔图标。

    ![“编辑”铅笔图标](common/edit-urls.png)

4. 在“基本 SAML 配置”窗格中完成以下步骤：

    ![Palo Alto Networks Captive Portal 的“基本 SAML 配置”窗格](common/idp-intiated.png)

   1. 对于“标识符”，请输入采用 `https://<customer_firewall_host_name>/SAML20/SP` 模式的 URL。

   2. 对于“回复 URL”，请输入采用 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 模式的 URL。

      > [!NOTE]
      > 请将此步骤中的占位符值更新为实际的标识符和回复 URL。 若要获取实际值，请联系 [Palo Alto Networks Captive Portal 客户端支持团队](https://support.paloaltonetworks.com/support)。

5. 在“SAML 签名证书”部分中的“联合元数据 XML”旁边，选择“下载”。 将下载的文件保存在计算机上。

    ![联合元数据 XML 下载链接](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>配置 Palo Alto Networks Captive Portal 单一登录

接下来，在 Palo Alto Networks Captive Portal 中设置单一登录：

1. 在另一个浏览器窗口中，以管理员身份登录到 Palo Alto Networks 网站。

2. 选择“设备”选项卡。

    ![Palo Alto Networks 网站上的“设备”选项卡](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在菜单中，依次选择“SAML 标识提供者”、“导入”。

    ![“导入”按钮](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在“SAML 标识提供者服务器配置文件导入”对话框中完成以下步骤：

    ![配置 Palo Alto 网络单一登录](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. 对于“配置文件名称”，请输入类似于 **AzureAD-CaptivePortal** 的名称。
    
    2. 在“标识提供者元数据”旁边，选择“浏览”。 选择在 Azure 门户中下载的 metadata.xml 文件。
    
    3. 选择“确定”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

接下来，在 Azure 门户中创建名为 *Britta Simon* 的测试用户：

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户”窗格中完成以下步骤：

    ![“用户”对话框](common/user-properties.png)

    1. 对于“姓名”，请输入 **BrittaSimon**。
  
    2. 对于“用户名”，请输入“BrittaSimon\@\<your_company_domain\>”。 例如，“BrittaSimon\@contoso.com”。

    3. 对于“密码”，请输入一个密码。 建议保留好所输入密码的记录。 可以选中“显示密码”复选框来显示密码。

    4. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

接下来，授予 Palo Alto Networks Captive Portal 的访问权限，使 Britta Simon 能够使用 Azure 单一登录：

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

2. 在“应用程序”列表中输入 **Palo Alto Networks Captive Portal**，然后选择该应用程序。

    ![“应用程序”列表中的“Palo Alto Networks Captive Portal”链接](common/all-applications.png)

3. 在菜单中选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”。 然后，在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”窗格中的“用户”列表内，选择“Britta Simon”。 选择“选择”。

6. 若要将角色值添加到 SAML 断言，请在“选择角色”窗格中，选择该用户的相关角色。 选择“选择”。

7. 在“添加分配”窗格中选择“分配”。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>创建 Palo Alto Networks Captive Portal 测试用户

接下来，在 Palo Alto Networks Captive Portal 中创建名为 *Britta Simon* 的用户。 Palo Alto Networks Captive Portal 支持默认已启用的实时用户预配功能。 在本部分无需完成任何任务。 如果 Palo Alto Networks Captive Portal 中不存在任何用户，身份验证后会创建一个新用户。

> [!NOTE]
> 若要手动创建用户，请联系 [Palo Alto Networks Captive Portal 客户端支持团队](https://support.paloaltonetworks.com/support)。

### <a name="test-single-sign-on"></a>测试单一登录 

Palo Alto Networks Captive Portal 安装在 Windows VM 上的防火墙后面。 若要在 Palo Alto Networks Captive Portal 中测试单一登录，请使用远程桌面协议 (RDP) 登录到该 Windows VM。 在 RDP 会话中，打开浏览器并转到任一网站。 此时会打开 SSO URL，并且系统会提示进行身份验证。 完成身份验证后，即可访问网站。

## <a name="additional-resources"></a>其他资源

若要了解更多信息，请参阅下列文章：

- [有关将 SaaS 应用与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory 中的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

