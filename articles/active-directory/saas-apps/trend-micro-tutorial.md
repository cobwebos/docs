---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Trend Micro Web Security (TMWS) 的集成 | Microsoft Docs
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
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Trend Micro Web Security (TMWS) 的集成

本教程介绍如何将 Trend Micro Web Security (TMWS) 与 Azure Active Directory (Azure AD) 集成。 将 Trend Micro Web Security (TMWS) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Trend Micro Web Security (TMWS)。
* 让用户使用其 Azure AD 帐户自动登录到 Trend Micro Web Security (TMWS)。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Trend Micro Web Security (TMWS) 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Trend Micro Web Security (TMWS) 支持 SP 发起的 SSO 
* 配置 Trend Micro Web Security (TMWS) 后，可以强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>从库中添加 Trend Micro Web Security (TMWS)

若要配置 Trend Micro Web Security (TMWS) 与 Azure AD 的集成，需要从库中将 Trend Micro Web Security (TMWS) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Trend Micro Web Security (TMWS)。  
1. 在结果面板中选择“Trend Micro Web Security (TMWS)”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>配置并测试 Trend Micro Web Security (TMWS) 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Trend Micro Web Security (TMWS) 的 Azure AD SSO。  若要使 SSO 发挥作用，需要在 Azure AD 用户与 Trend Micro Web Security (TMWS) 中的相关用户之间建立链接关系。

若要配置并测试 Trend Micro Web Security (TMWS) 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
    1. **[在 Azure AD 中配置用户和组同步设置](#configure-user-and-group-synchronization-settings-in-azure-ad)** - 在 Azure AD 中配置用户和组同步设置
1. **[配置 Trend Micro Web Security (TMWS) SSO](#configure-trend-micro-web-security-sso)** - 在应用程序端配置单一登录设置。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Trend Micro Web Security (TMWS)”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. 在“回复 URL”文本框中键入 URL：`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 标识符非实际值。 请使用实际标识符更新此值。 可以在 Azure AD 的“身份验证方法”屏幕的“Azure 管理门户的服务提供商设置”区域下获取这些值，只需单击“管理”>“目录服务”即可。   

1. Trend Micro Web Security (TMWS) 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Trend Micro Web Security (TMWS) 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Trend Micro Web Security (TMWS)”部分，根据需要复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Trend Micro Web Security (TMWS) 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“Trend Micro Web Security (TMWS)”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>在 Azure AD 中配置用户和组同步设置

1. 在左侧导航中，单击“Azure Active Directory”。 

1. 在“管理”下单击“应用注册”，然后在“所有应用程序”区域下单击新的企业应用程序。   

1. 在“管理”下，单击“证书和机密”。  

1. 在出现的“客户端机密”区域下，单击“新建客户端机密”。 

1. 在出现的“添加客户端机密”屏幕上，为此客户端机密选择性地添加说明并选择过期期限，然后单击“添加”。  新添加的客户端机密将出现在“客户端机密”区域下。

1. 请记录该值。 稍后需要在 TMWS 中键入此信息。

1. 在“管理”下，单击“API 权限”。   

1. 在出现的“API 权限”屏幕上，单击“添加权限”。 

1. 在出现的“请求 API 权限”屏幕的“Microsoft API”选项卡上，依次单击“Microsoft Graph”、“应用程序权限”。  

1. 找到并添加以下权限： 

    * Group.Read.All
    * User.Read.All

1. 单击“添加权限”。  此时将出现一条消息，确认已成功保存设置。 新添加的权限将出现在“API 权限”屏幕上。

1. 在“授予同意”区域下，依次单击“为 <管理员帐户> (默认目录)授予管理员同意”、“是”。   此时会出现一条消息，确认已成功授予对所请求权限的管理员同意。

1. 单击“概览”。  

1. 将所出现的右侧窗格中的“应用程序(客户端) ID”和“目录(租户) ID”记录下来。 稍后需要在 TMWS 中键入此信息。 还可以单击“Azure Active Directory”>“管理”下的“自定义域名”，然后记录右侧窗格中的域名。  

## <a name="configure-trend-micro-web-security-sso"></a>配置 Trend Micro Web Security SSO

1. 登录到 TMWS 管理控制台，转到“管理” > “用户和身份验证” > “目录服务”。   

1. 在屏幕的上部区域单击此处。

1. 在出现的“身份验证方法”屏幕上，单击“Azure AD”  。

1. 单击“开启”  或“关闭”  来决定当组织的 AD 用户的数据未同步到 TMWS 时，是否允许这些用户通过 TMWS 访问网站。

    > [!NOTE]
    > 未从 Azure AD 同步的用户只能通过已知的 TMWS 网关或组织的专用端口进行身份验证。

1. 在“标识提供者设置”  部分中，执行以下步骤：

    a. 在“服务 URL”字段中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在“登录名属性”  字段中，粘贴 Azure 门户提供的源属性为“user.onpremisessamaccountname”的“用户声明名称”。 

    c. 在“公共 SSL 证书”  字段中，使用从 Azure 门户下载的“证书(Base64)”  。

1. 在“同步设置”  部分中，执行以下步骤：

    a. 在“租户”  字段中，使用来自 Azure 门户的“目录(租户) ID”  或“自定义域名”  值。

    b. 在“应用程序 ID”字段中，使用来自 Azure 门户的“应用程序(客户端)ID”值。  

    c. 在“客户端机密”  字段中，使用来自 Azure 门户的“客户端机密”  。

    d. 在“同步计划”  字段中，选择手动或按计划与 Azure AD 同步。 如果选择“手动”，则每当存在 Active Directory 用户信息更改时，请记得返回到“目录服务”屏幕并执行手动同步，使 TMWS 中的信息保持最新。

    e. 单击“测试连接”  ，检查 Azure AD 服务是否可以成功连接。 
    
    f. 单击“保存”  。
 
 > [!NOTE]
 > 有关如何通过 Azure AD 配置 Trend Micro Web 安全性的详细信息，请参阅[此](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)文档。

## <a name="test-sso"></a>测试 SSO 

成功配置 Azure AD 服务并将 Azure AD 指定为用户身份验证方法后，可以登录到 TMWS 代理服务器来验证设置。 在 Azure AD 登录验证你的帐户后，即可访问 Internet。

> [!NOTE]
> TMWS 不支持通过 Azure AD 门户中的“概述”>“单一登录”>“设置 SAML 单一登录”>“新企业应用程序测试”来测试单一登录。

1. 清除浏览器中的所有 Cookie，然后重启浏览器。 

1. 将浏览器指向 TMWS 代理服务器。 有关详细信息，请参阅[使用 PAC 文件转发流量](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)。

1. 访问任何 Internet 网站。 TMWS 会将你转到 TMWS 强制网络门户。

1. 指定 Active Directory 帐户（格式：domain\sAMAccountName 或 sAMAccountName@domain）、电子邮件地址或 UPN，然后单击“登录”。  TMWS 会将你转到 Azure AD 登录页。

1. 在 Azure AD 登录页上，键入 AD 帐户凭据。 应该可以成功登录到 TMWS。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Trend Micro Web Security (TMWS)](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Trend Micro Web Security(TMWS)](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

