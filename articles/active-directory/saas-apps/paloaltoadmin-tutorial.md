---
title: 教程：Azure Active Directory 与 Palo Alto Networks - 管理 UI 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks - 管理 UI 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: fed368c0df265495d9fee764f86825957fae8bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447418"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>将 Azure Active Directory 与 Palo Alto Networks - Admin UI 集成

本教程介绍如何将 Azure Active Directory (Azure AD) 与 Palo Alto Networks - Admin UI 集成。

将 Azure AD 与 Palo Alto Networks - Admin UI 集成可提供以下好处：

- 可在 Azure AD 中控制谁有权访问 Palo Alto Networks - 管理 UI。
- 可让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks - Admin UI（单一登录或 SSO）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks - 管理 UI 的集成，需具有以下项目：

- Azure AD 订阅
- Palo Alto Networks 新一代防火墙或 Panorama（针对防火墙的集中管理系统）

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 

本教程中概述的方案包括两个主要构建基块：

* 从库添加 Palo Alto Networks - 管理 UI
* 配置和测试 Azure AD 单一登录

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>从库添加 Palo Alto Networks - Admin UI
若要配置 Palo Alto Networks - Admin UI 与 Azure AD 的集成，需要执行以下操作，从库中将 Palo Alto Networks - Admin UI 添加到托管的 SaaS 应用列表：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

1. 选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗口][2]
    
1. 要添加新应用程序，请选择窗口顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入 **Palo Alto Networks - Admin UI**，从结果面板选择“Palo Alto Networks - Admin UI”，然后选择“添加”。

    ![结果列表中的 Palo Alto Networks - 管理 UI](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，通过名为“Britta Simon”的测试用户，配置和测试 Palo Alto Networks - Admin UI 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 中的用户相对应的 Palo Alto Networks - Admin UI 用户。 换句话说，必须建立 Azure AD 用户与 Palo Alto Networks - Admin UI 中同一用户之间的链接关系。

若要建立链接关系，请将 Azure AD 中“用户名”的值指定为 Palo Alto Networks - Admin UI 中“用户名”的值。

要配置和测试 Palo Alto Networks - Admin UI 的 Azure AD 单一登录，请完成下面 5 个部分中的构建基块。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

执行以下操作，在 Azure 门户中启用 Azure AD 单一登录，并在 Palo Alto Networks - Admin UI 应用程序中配置单一登录：

1. 在 Azure 门户中的“Palo Alto Networks - Admin UI”应用程序集成页上，选择“单一登录”。

    ![“单一登录”链接][4]

1. 在“单一登录”窗口中的“单一登录模式”框内，选择“基于 SAML 的登录”。
 
    ![“单一登录”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

1. 在“Palo Alto Networks - Admin UI 域和 URL”下面执行以下操作：

    ![“Palo Alto Networks - Admin UI 域和 URL”单一登录信息](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. 在“登录 URL”文本框中，使用以下格式键入 URL：*https://\<客户防火墙 FQDN>/php/login.php*。

    b. 在“标识符”文本框中，使用以下格式键入 URL：*https://\<客户防火墙 FQDN>:443/SAML20/SP*。
    
    c. 在“回复 URL”框中，使用以下格式键入断言使用者服务 (ACS) URL：*https://\<客户防火墙 FQDN>:443/SAML20/SP/ACS*。
    
    > [!NOTE] 
    > 上面的值不是实际值。 使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [Palo Alto Networks - Admin UI 客户端支持团队](https://support.paloaltonetworks.com/support)。 
 
1. Palo Alto Networks - Admin UI 应用程序需要采用特定格式的 SAML 断言，因此请按下图所示配置声明。 执行以下步骤，在“应用程序集成”页的“用户属性”部分中管理属性值：
    
    ![SAML 令牌属性列表](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > 由于属性值仅为示例，请为 *username* 和 *adminrole* 映射相应的值。 另外还有一个可选属性 *accessdomain* 用于限制管理员访问防火墙上的特定虚拟系统。
   >
        
    | 属性名称 | 属性值 |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. 选择“添加属性”。  
    
    ![“添加属性”按钮](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    此时会打开“添加属性”窗口。

    ![“添加属性”窗口](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”框中，键入为该行显示的属性名称。
    
    c. 在“值”框中，键入为该行显示的属性值。
    
    d. 选择“确定”。

    > [!NOTE]
    > 有关这些属性的详细信息，请参阅以下文章：
    > * [Admin UI 的管理角色配置文件 (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI 的设备访问域 (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

1. 在“SAML 签名证书”下面选择“元数据 XML”，然后选择“保存”。

    ![元数据 XML 下载链接](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![点击“保存”按钮](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

1. 在新窗口中以管理员身份打开 Palo Alto Networks Firewall Admin UI。

1. 选择“设备”选项卡。

    ![“设备”选项卡](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

1. 在左窗格中选择“SAML 标识提供者”，然后选择“导入”以导入元数据文件。

    ![“导入元数据文件”按钮](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

1. 在“SAML 标识提供者服务器配置文件导入”窗口中执行以下操作：

    ![“SAML 标识提供者服务器配置文件导入”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. 在“配置文件名称”框中提供一个名称（例如 **AzureAD Admin UI**）。
    
    b. 在“标识提供者元数据”下面选择“浏览”，然后选择前面从 Azure 门户下载的 metadata.xml 文件。
    
    c. 取消选中“验证标识提供者证书”复选框。
    
    d. 选择“确定”。
    
    e. 若要提交有关防火墙的配置，请选择“提交”。

1. 在左窗格中选择“SAML 标识提供者”，然后选择在上一步骤中创建的 SAML 标识提供者配置文件（例如 **AzureAD Admin UI**）。 

    ![SAML 标识提供者配置文件](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

1. 在“SAML 标识提供者服务器配置文件”窗口中执行以下操作：

    ![“SAML 标识提供者服务器配置文件”窗口](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. 在“标识提供者 SLO URL”框中，将前面导入的 SLO URL 替换为以下 URL：**https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**
  
    b. 选择“确定”。

1. 在 Palo Alto Networks Firewall's Admin UI 中选择“设备”，然后选择“管理员角色”。

1. 选择“添加”按钮。 

1. 在“管理员角色配置文件”窗口中的“名称”框内，为管理员角色提供一个名称（例如 **fwadmin**）。  
    此管理员角色名称应与标识提供者发送的 SAML 管理员角色属性名称匹配。 管理员角色名称和值已在步骤 4 中创建。

    ![配置 Palo Alto 网络管理员角色](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
1. 在 Firewall's Admin UI 中选择“设备”，然后选择“身份验证配置文件”。

1. 选择“添加”按钮。 

1. 在“身份验证配置文件”窗口中，执行以下操作： 

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

1. 若要使管理员能够通过 Azure 使用 SAML SSO，请选择“设备” > “设置”。 在“设置”窗格中选择“管理”选项卡，然后在“身份验证设置”下面选择“设置”（齿轮图标）按钮。 

 ![“设置”按钮](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

1. 选择在步骤 17 中创建的 SAML 身份验证配置文件（例如 **AzureSAML_Admin_AuthProfile**）。

 ![“身份验证配置文件”字段](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

1. 选择“确定”。

1. 若要提交配置，请选择“提交”。


> [!TIP]
> 设置应用时，可以在 [Azure 门户](https://portal.azure.com)中阅读前述教程的简洁版本。 在“Active Directory” > “企业应用程序”部分添加此应用后，请选择“单一登录”选项卡，然后在底部的“配置”部分访问嵌入式文档。 有关嵌入式文档功能的详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

![创建 Azure AD 测试用户][100]

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”链接](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. 若要显示当前用户列表，请选择“用户和组” > “所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. 在“所有用户”窗口的顶部，选择“添加”。

    ![“添加”按钮](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    此时会打开“用户”窗口。

1. 在“用户”窗口中执行以下操作：

    ![“用户”窗口](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>创建 Palo Alto Networks - 管理 UI 测试用户

Palo Alto Networks - Admin UI 支持实时用户预配。 如果用户尚不存在，在身份验证成功后会自动创建该用户。 无需执行创建用户的操作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，我们通过授予用户 Britta Simon 访问 Palo Alto Networks - Admin UI 的权限，使其能够使用 Azure 单一登录。 为此，请执行以下操作：

![分配用户角色][200] 

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，然后选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”和“所有应用程序”链接][201] 

1. 在“应用程序”列表中，选择“Palo Alto Networks - Admin UI”。

    ![Palo Alto Networks - Admin UI 链接](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

1. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202]

1. 选择“添加”，然后在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”窗口中的“用户”列表内，选择“Britta Simon”。

1. 选择“选择”按钮。

1. 在“添加分配”窗口中，选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Palo Alto Networks - Admin UI”磁贴时，应会自动登录到 Palo Alto Networks - Admin UI 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

