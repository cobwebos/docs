---
title: 教程：Azure AD 与 Trend Micro Web Security (TMWS) 的 SSO 集成
description: 了解如何在 Azure Active Directory 与 Trend Micro Web Security (TMWS) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09af492439beb931b4ab1cd08ccb9e1d82fc3f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>教程：Azure Active Directory 与 Trend Micro Web Security (TMWS) 的单一登录 (SSO) 集成

本教程介绍如何将 Trend Micro Web Security (TMWS) 与 Azure Active Directory (Azure AD) 集成。 将 TMWS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 TMWS。
* 让用户使用其 Azure AD 帐户自动登录到 TMWS。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 SSO 的 TMWS 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* TMWS 支持 SP 发起的 SSO。
* 配置 TMWS 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 若要了解如何使用 Microsoft Cloud App Security 实施会话控制，请参阅[为任何应用加入和部署条件访问应用控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-tmws-from-the-gallery"></a>从库中添加 TMWS

若要配置 TMWS 与 Azure AD 的集成，需要从库中将 TMWS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中，选择“Azure Active Directory”服务。 
1. 依次选择“企业应用程序”、“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中输入“Trend Micro Web Security (TMWS)”。  
1. 在搜索结果中选择“Trend Micro Web Security (TMWS)”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>配置并测试 TMWS 的 Azure AD SSO

你将使用名为 B.Simon 的测试用户配置并测试 TMWS 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 TMWS 中的相关用户之间建立链接。

你将完成以下基本步骤来配置并测试 TMWS 的 Azure AD SSO：

1. [配置 Azure AD SSO](#configure-azure-ad-sso) 来为用户启用该功能。
    1. [创建 Azure AD 用户](#create-an-azure-ad-test-user)来测试 Azure AD 单一登录。
    1. [向 Azure AD 测试用户授予](#grant-the-azure-ad-test-user-access-to-tmws)对 TMWS 的访问权限。
    1. [在 Azure AD 中配置用户和组同步设置](#configure-user-and-group-synchronization-settings-in-azure-ad)。
1. 在应用程序端[配置 TMWS SSO](#configure-tmws-sso)。
1. [测试 SSO](#test-sso) 以验证配置。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在 Azure 门户中完成以下步骤以启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“Trend Micro Web Security (TMWS)”应用程序集成页上，在“管理”部分中选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形按钮以编辑设置：  

   ![编辑“基本 SAML 配置”设置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，在以下框中输入值： 

    a. 在“标识符(实体 ID)”框中，使用以下模式输入 URL： 

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. 在“回复 URL”中输入以下 URL： 

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 要输入的值并不是在上一步骤中获取的标识符值。 需要使用实际标识符。 可以在 Azure AD 的“身份验证方法”页上的“Azure 管理门户的服务提供商设置”部分中，从“管理”>“目录服务”获取此值。   

1. TMWS 需要特定格式的 SAML 断言，因此需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性：

    ![默认属性](common/default-attributes.png)

1. 除上面的屏幕截图中所示的属性以外，TMWS 还要求在 SAML 响应中传回其他两个属性。 下表显示了这些属性。 这些属性已预先填充，但你可以根据需要对其进行更改。
    
    | 名称 | 源属性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”    。 选择此证书名称旁边的“下载”链接，以下载证书并将其保存在计算机上： 

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Trend Micro Web Security (TMWS)”部分中，根据你的需要复制相应的一个或多个 URL： 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。  选择“用户”，然后选择“所有用户”   。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”框中，输入 `B.Simon`。   
   1. 在“用户名”框中，输入 ***username *@* companydomain *.* extension***。  例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>向 Azure AD 测试用户授予对 TMWS 的访问权限

在本部分中，你将通过授予 B.Simon 访问 TMWS 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“Trend Micro Web Security (TMWS)”。 
1. 在应用概览页上，在“管理”部分中选择“用户和组”：  

   ![选择“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击屏幕底部的“选择”按钮。    
1. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中选择“分配”。  

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>在 Azure AD 中配置用户和组同步设置

1. 在左窗格中选择“Azure Active Directory”  。

1. 在“管理”下选择“应用注册”，然后在“所有应用程序”下选择你的新企业应用程序。   

1. 在“管理”下，选择“证书和机密”。  

1. 在“客户端机密”区域中，选择“新建客户端机密”。  

1. 在“添加客户端机密”屏幕上，为客户端机密添加说明（可选）并选择有效期，然后选择“添加”。   新客户端机密将显示在“客户端机密”区域中。 

1. 请记下客户端机密值。 稍后需要将其输入到 TMWS 中。

1. 在“管理”下选择“API 权限”  。  

1. 在“API 权限”窗口中，选择“添加权限”。  

1. 在“请求 API 权限”窗口的“Microsoft API”选项卡上，依次选择“Microsoft Graph”、“应用程序权限”。    

1. 找到并添加以下权限： 

    * Group.Read.All
    * User.Read.All

1. 选择“添加权限”  。 此时会出现一条消息，确认已保存设置。 新权限将显示在“API 权限”窗口中。 

1. 在“授予许可”区域中，选择“为 <管理员帐户> (默认目录)授予管理员许可”，然后选择“是”。    此时会出现一条消息，确认已授予对所请求权限的管理员许可。

1. 选择“概述”。  

1. 记下右侧窗格中显示的“应用程序(客户端) ID”和“目录(租户) ID”。   稍后需要在 TMWS 中输入该信息。 还可以选择“Azure Active Directory”>“管理”下的“自定义域名”，然后记下右侧窗格中显示的域名。  

## <a name="configure-tmws-sso"></a>配置 TMWS SSO

完成以下步骤以在应用程序端配置 TMWS SSO。

1. 登录到 TMWS 管理控制台，转到“管理” > “用户和身份验证” > “目录服务”。   

1. 在屏幕的上部区域选择“此处”  。

1. 在“身份验证方法”页面上，选择“Azure AD”。  

1. 选择“打开”  或“关闭”  来配置当组织中 Azure AD 用户的数据未同步到 TMWS 时，是否允许这些用户通过 TMWS 访问网站。

    > [!NOTE]
    > 未从 Azure AD 同步的用户只能通过已知的 TMWS 网关或组织的专用端口进行身份验证。

1. 在“标识提供者设置”部分完成以下步骤： 

    a. 在“服务 URL”框中，输入从 Azure 门户复制的“登录 URL”值   。

    b. 在“登录名属性”  框中，输入具有来自 Azure 门户的“user.onpremisessamaccountname”源属性的“用户声明名称”。  

    c. 在“公共 SSL 证书”  框中，使用从 Azure 门户下载的“证书(Base64)”  。

1. 在“同步设置”部分中，完成以下步骤： 

    a. 在“租户”  框中，输入来自 Azure 门户的“目录(租户) ID”  或“自定义域名”  值。

    b. 在“应用程序 ID”框中，输入来自 Azure 门户的“应用程序(客户端)ID”值。  

    c. 在“客户端机密”  框中，输入来自 Azure 门户的“客户端机密”  。

    d. 选择“同步计划”  ，以手动或按计划与 Azure AD 同步。 如果选择“手动”，则每当存在 Active Directory 用户信息更改时，请记得返回到“目录服务”页并执行手动同步，使 TMWS 中的信息保持最新。  

    e. 选择“测试连接”  ，检查 Azure AD 服务是否可以成功连接。
    
    f. 选择“保存”。 
 
 > [!NOTE]
 > 有关如何使用 Azure AD 配置 TMWS 的详细信息，请参阅[在 TMWS 中配置 Azure AD 设置](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)。

## <a name="test-sso"></a>测试 SSO 

配置 Azure AD 服务并将 Azure AD 指定为用户身份验证方法后，可以登录到 TMWS 代理服务器来验证设置。 在 Azure AD 登录验证你的帐户后，即可访问 Internet。

> [!NOTE]
> TMWS 不支持通过 Azure AD 门户中的“概览”   > “单一登录”   > “设置 SAML 单一登录”   > “新企业应用程序测试”  来测试单一登录。

1. 清除浏览器中的所有 Cookie，然后重启浏览器。 

1. 将浏览器指向 TMWS 代理服务器。 有关详细信息，请参阅[使用 PAC 文件转发流量](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)。

1. 访问任何 Internet 网站。 TMWS 会将你转到 TMWS 强制网络门户。

1. 指定 Active Directory 帐户（格式：*domain*\\*sAMAccountName* 或 *sAMAccountName*@*domain*）、电子邮件地址或 UPN，然后选择“登录”  。 TMWS 会将你转到 Azure AD 登录窗口。

1. 在 Azure AD 登录窗口中，输入你的 Azure AD 帐户凭据。 现在你应当已登录到 TMWS。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 Trend Micro Web Security](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Trend Micro Web Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

