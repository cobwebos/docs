---
title: 教程：Azure Active Directory 与 Palo Alto Networks - 管理 UI 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks - 管理 UI 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82560d0767b6865dded3e14e661fe89b7132ab95
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869847"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>教程：Azure Active Directory 与 Palo Alto Networks - 管理 UI 的集成

在本教程中，了解如何将 Palo Alto Networks - 管理 UI 与 Azure Active Directory (Azure AD) 进行集成。
将 Palo Alto Networks - 管理 UI 与 Azure AD 集成可提供以下好处：

* 可在 Azure AD 中控制谁有权访问 Palo Alto Networks - 管理 UI。
* 可以让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks - 管理 UI（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks - 管理 UI 的集成，需具有以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了单一登录的 Palo Alto Networks - 管理 UI 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Palo Alto Networks - 管理 UI 支持 **SP** 发起的 SSO
* Palo Alto Networks - 管理 UI 支持**实时**用户预配

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>从库添加 Palo Alto Networks - 管理 UI

若要配置 Palo Alto Networks - 管理 UI 与 Azure AD 的集成，需要从库中将 Palo Alto Networks - 管理 UI 添加到托管的 SaaS 应用列表。

若要从库中添加 Palo Alto Networks - 管理 UI，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Palo Alto Networks - 管理 UI”，从结果面板选择“Palo Alto Networks - 管理 UI”，然后单击“添加”按钮来添加应用程序。

     ![结果列表中的 Palo Alto Networks - 管理 UI](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Palo Alto Networks - 管理 UI 中相关用户之间建立链接关系。

若要配置和测试 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Palo Alto Networks - 管理 UI 单一登录](#configure-palo-alto-networks---admin-ui-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Palo Alto Networks - 管理 UI 测试用户](#create-palo-alto-networks---admin-ui-test-user)** - 在 Palo Alto Networks - 管理 UI 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Palo Alto Networks - 管理 UI** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Palo Alto Networks - 管理 UI 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<Customer Firewall FQDN>/php/login.php`。

    b. 在“标识符”框中，使用以下模式键入 URL：`https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. 在“回复 URL”文本框中，使用以下格式键入断言使用者服务 (ACS) URL：`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 若要获取这些值，请联系 [Palo Alto Networks - 管理 UI 客户端支持团队](https://support.paloaltonetworks.com/support)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. Palo Alto Networks - 管理 UI 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

   > [!NOTE]
   > 由于属性值仅为示例，请为 *username* 和 *adminrole* 映射相应的值。 另外还有一个可选属性 *accessdomain* 用于限制管理员访问防火墙上的特定虚拟系统。
   >

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 |  源属性|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

    > [!NOTE]
    > 有关这些属性的详细信息，请参阅以下文章：
    > * [Admin UI 的管理角色配置文件 (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI 的设备访问域 (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

8. 在“设置 Palo Alto Networks - 管理 UI”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>配置 Palo Alto Networks - 管理 UI 单一登录

1. 在新窗口中以管理员身份打开 Palo Alto Networks Firewall Admin UI。

2. 选择“设备”选项卡。

    ![“设备”选项卡](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在左窗格中选择“SAML 标识提供者”，然后选择“导入”以导入元数据文件。

    ![“导入元数据文件”按钮](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在“SAML 标识提供者服务器配置文件导入”窗口中执行以下操作：

    ![“SAML 标识提供者服务器配置文件导入”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. 在“配置文件名称”框中提供一个名称（例如 **AzureAD Admin UI**）。
    
    b. 在“标识提供者元数据”下面选择“浏览”，然后选择前面从 Azure 门户下载的 metadata.xml 文件。
    
    c. 取消选中“验证标识提供者证书”复选框。
    
    d. 选择“确定”。
    
    e. 若要提交有关防火墙的配置，请选择“提交”。

5. 在左窗格中选择“SAML 标识提供者”，然后选择在上一步骤中创建的 SAML 标识提供者配置文件（例如 **AzureAD Admin UI**）。

    ![SAML 标识提供者配置文件](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. 在“SAML 标识提供者服务器配置文件”窗口中执行以下操作：

    ![“SAML 标识提供者服务器配置文件”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. 在“标识提供者 SLO URL”框中，将前面导入的 SLO URL 替换为以下 URL：`https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. 选择“确定”。

7. 在 Palo Alto Networks Firewall's Admin UI 中选择“设备”，然后选择“管理员角色”。

8. 选择“添加”按钮。

9. 在“管理员角色配置文件”窗口中的“名称”框内，为管理员角色提供一个名称（例如 **fwadmin**）。 此管理员角色名称应与标识提供者发送的 SAML 管理员角色属性名称匹配。 管理员角色名称和值是在 Azure 门户中的“用户属性”部分中创建的。

    ![配置 Palo Alto 网络管理员角色](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. 在 Firewall's Admin UI 中选择“设备”，然后选择“身份验证配置文件”。

11. 选择“添加”按钮。

12. 在“身份验证配置文件”窗口中，执行以下操作： 

    ![“身份验证配置文件”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. 在“名称”框中提供一个名称（例如 **AzureSAML_Admin_AuthProfile**）。

    b. 在“类型”下拉列表中选择“SAML”。 

    c. 在“IdP 服务器配置文件”下拉列表中，选择合适的 SAML 标识提供者服务器配置文件（例如 **AzureAD Admin UI**）。

    c. 选中“启用单一注销”复选框。

    d. 在“管理员角色属性”框中输入属性名称（例如 **adminrole**）。

    e. 选择“高级”选项卡，然后在“允许列表”下面选择“添加”。

    ![“高级”选项卡上的“添加”按钮](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. 选中“所有”复选框，或者选择可以通过此配置文件进行身份验证的用户和组。  
    当用户进行身份验证时，防火墙将根据此列表中的条目来匹配关联的用户名或组。 如果你未添加条目，则没有用户可以进行身份验证。

    g. 选择“确定”。

13. 若要使管理员能够通过 Azure 使用 SAML SSO，请选择“设备” > “设置”。 在“设置”窗格中选择“管理”选项卡，然后在“身份验证设置”下面选择“设置”（齿轮图标）按钮。

    ![“设置”按钮](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. 选择在“身份验证配置文件”窗口中创建的 SAML 身份验证配置文件（例如 **AzureSAML_Admin_AuthProfile**）。

    ![“身份验证配置文件”字段](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. 选择“确定”。

16. 若要提交配置，请选择“提交”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Palo Alto Networks - 管理 UI 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Palo Alto Networks - 管理 UI”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Palo Alto Networks - 管理 UI”。

    ![应用程序列表中的 Palo Alto Networks - 管理 UI 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>创建 Palo Alto Networks - 管理 UI 测试用户

Palo Alto Networks - Admin UI 支持实时用户预配。 如果用户尚不存在，在身份验证成功后会自动创建该用户。 无需执行创建用户的操作。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Palo Alto Networks - 管理 UI 磁贴时，应当会自动登录到你为其设置了 SSO 的 Palo Alto Networks - 管理 UI。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
