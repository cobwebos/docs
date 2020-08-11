---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Kemp LoadMaster Azure AD 集成的集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 和 Kemp LoadMaster Azure AD 集成之间的单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f42e1731-0477-4955-9571-b69a0f3de0bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a0afdecfcab9cc1ab3903429b197fe859d40a7f
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424560"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>教程：Azure Active Directory SSO 与 Kemp LoadMaster Azure AD 集成的集成

本教程介绍如何将 Kemp LoadMaster Azure AD 集成与 Azure Active Directory (Azure AD) 集成。 将 Kemp LoadMaster Azure AD 集成与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Kemp LoadMaster Azure AD 集成。
* 让用户使用其 Azure AD 帐户自动登录到 Kemp LoadMaster Azure AD 集成。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Kemp LoadMaster Azure AD 集成单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Kemp LoadMaster Azure AD 集成支持 IDP 发起的 SSO
* 配置 Kemp LoadMaster Azure AD 集成后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>从库中添加 Kemp LoadMaster Azure AD 集成

要配置 Kemp LoadMaster Azure AD 集成与 Azure AD 的集成，需要从库中将 Kemp LoadMaster Azure AD 集成添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Kemp LoadMaster Azure AD 集成”。 
1. 在结果面板中选择“Kemp LoadMaster Azure AD 集成”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>为 Kemp LoadMaster Azure AD 集成配置和测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Kemp LoadMaster Azure AD 集成的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Kemp LoadMaster Azure AD 集成相关用户之间建立关联。

若要配置并测试 Kemp LoadMaster Azure AD 集成的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Kemp LoadMaster Azure AD 集成 SSO](#configure-kemp-loadmaster-azure-ad-integration-sso) - 在应用程序端配置单一登录设置。

1. [发布 Web 服务器](#publishing-web-server)
    1. [创建虚拟服务](#create-a-virtual-service)
    1. [证书和安全性](#certificates-and-security)
    1. [Kemp LoadMaster Azure AD 集成 SAML 配置文件](#kemp-loadmaster-azure-ad-integration-saml-profile)
    1. [验证更改](#verify-the-changes)
1. [配置基于 Kerberos 的身份验证](#configuring-kerberos-based-authentication)
    1. [为 Kemp LoadMaster Azure AD 集成创建 Kerberos 委派帐户](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)
    1. [Kemp LoadMaster Azure AD 集成 KCD（Kerberos 委派帐户）](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)
    1. [Kemp LoadMaster Azure AD 集成 ESP](#kemp-loadmaster-azure-ad-integration-esp)
    1. [创建 Kemp LoadMaster Azure AD 集成测试用户](#create-kemp-loadmaster-azure-ad-integration-test-user) - 在 Kemp LoadMaster Azure AD 集成中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Kemp LoadMaster Azure AD 集成”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，键入 URL：`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. 在“回复 URL”文本框中键入 URL：`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Kemp LoadMaster Azure AD 集成客户端支持团队](mailto:support@kemp.ax)获取这些值。 还可参考 Azure 门户的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“证书(Base64)”和“联合元数据 XML”，选择“下载”以下载证书和联合元数据 XML 文件并将其保存在计算机上    。

    ![证书下载链接](./media/kemp-tutorial/certificate-base-64.png)

1. 在“设置 Kemp LoadMaster Azure AD 集成”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分，你将通过授予 B.Simon 访问 Kemp LoadMaster Azure AD 集成的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Kemp LoadMaster Azure AD 集成”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>配置 Kemp LoadMaster Azure AD 集成 SSO

## <a name="publishing-web-server"></a>发布 Web 服务器 

### <a name="create-a-virtual-service"></a>创建虚拟服务 

1. 转到 Kemp LoadMaster Azure AD 集成 Load Master Web UI >“虚拟服务”>“新增”。

1. 单击“新增”。

1. 指定虚拟服务的参数。

    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-1.png)

    a. 虚拟地址
    
    b. 端口
    
    c. 服务名称（可选）
    
    d. 协议 

1. 导航到“实际服务器”部分。

1. 单击“新增”。

1. 指定实际服务器的参数。
    
    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-2.png)

    a. 选择“允许远程地址”
    
    b. 键入实际服务器地址
    
    c. 端口
    
    d. 转发方法
    
    e. 重量
    
    f. 连接限制
    
    g. 单击“添加此实际服务器”

## <a name="certificates-and-security"></a>证书和安全性
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>在 Kemp LoadMaster Azure AD 集成上导入证书 
 
1. 进入 Kemp LoadMaster Azure AD 集成 Web 门户 >“证书和安全性”>“SSL 证书”。

1. 在“管理证书”>“证书配置”下。

1. 单击“导入证书”。

1. 指定包含证书的文件的名称。 文件还可以包含私钥。 如果文件不包含私钥，则还必须指定包含私钥的文件。 证书可以采用 .PEM 或 .PFX (IIS) 格式。

1. 在“证书文件”上单击“选择文件”。

1. 单击“密钥文件(可选)”。

1. 单击“保存”。

### <a name="ssl-acceleration"></a>SSL 加速
 
1. 转到 Kemp Load Master Web UI >“虚拟服务”>“虚拟服务”。

1. 在“操作”下单击“修改”。

1. 在“tcp/x.x.x.:443 (ID:6)的属性”下（在第 7 层运行），单击“SSL 属性”。
    
    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-3.png)
    
    a. 在“SSL 加速”中单击“已启用”。
    
    b. 在“可用证书”下，选择导入的证书并单击 `>` 符号。
    
    c. 在所需 SSL 证书出现在“分配的证书”中后，单击“设置证书”。

    > [!NOTE]
    > 确保单击“设置证书”。

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster Azure AD 集成 SAML 配置文件
 
### <a name="import-idp-certificate"></a>导入 IdP 证书

转到 Kemp LoadMaster Azure AD 集成 Web 控制台 

1. 在“证书和颁发机构”下单击“中间证书”。

    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-6.png)

    a. 在“添加新的中间证书”中单击“选择文件”。
    
    b. 导航到以前从 Azure AD 企业应用程序下载的证书文件。
    
    c. 单击“打开”。
    
    d. 在“证书名称”中提供名称。
    
    e. 单击“添加证书”。

### <a name="create-authentication-policy"></a>创建身份验证策略 
 
转到“虚拟服务”下的“管理 SSO”。

   ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-7.png)
   
   a. 在为其提供名称之后，单击“添加新客户端配置”下的“添加”。

   b. 在“身份验证协议”下选择“SAML”。

   c. 在“IdP 预配”下选择“元数据文件”。 

   d. 单击“选择文件”。

   e. 导航到以前从 Azure 门户下载的 XML。

   f. 单击“打开”，然后单击“导入 IdP 元数据文件”。

   g. 从“IdP 证书”中选择中间证书。

   h. 设置应与 Azure 门户中创建的标识匹配的 SP 实体 ID 

   i. 单击“设置 SP 实体 ID”。

### <a name="set-authentication"></a>设置身份验证  
 
在 Kemp LoadMaster Azure AD 集成 Web 控制台上

1. 单击“虚拟服务”。

1. 单击“查看/修改服务”。

1. 单击“修改”，然后导航到“ESP 选项”。
    
    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-8.png)

    a. 单击“启用 ESP”。
    
    b. 在“客户端身份验证模式”中选择“SAML”。
    
    c. 选择以前在 SSO 域中创建的客户端身份验证。
    
    d. 在“允许的虚拟主机”中键入主机名，然后单击“设置允许的虚拟主机”。
    
    e. 在“允许的虚拟目录(基于访问要求)”中键入 /*，然后单击“设置允许的目录”。

### <a name="verify-the-changes"></a>验证更改 
 
浏览到应用程序 URL 

此时应显示租户登录页，而不是之前未经身份验证的访问。 

![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>配置基于 Kerberos 的身份验证 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>为 Kemp LoadMaster Azure AD 集成创建 Kerberos 委派帐户 

1. 创建用户帐户（在此示例中为 AppDelegation）。
    
    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-10.png)


    a. 选择“属性编辑器”选项卡。

    b. 导航到 servicePrincipalName。 

    c. 选择 servicePrincipalName，然后单击“编辑”。

    d. 在“要添加的值”字段中键入 http/kcduser，然后单击“添加”。 

    e. 单击“应用”和“确定”。 必须先关闭窗口，然后再次打开（以查看新的“委派”选项卡）。 

1. 再次打开用户属性窗口，“委派”选项卡会变为可用。 

1. 选择“委派”选项卡。

    ![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-11.png)

    a. 选择“仅信任此用户作为指定服务的委派”。

    b. 选择“使用任何身份验证协议”。

    c. 添加实际服务器并将 http 添加为服务。 
    
    d. 选中“扩展”复选框。 

    e. 你可以看到所有服务器以及有主机名和 FQDN。
    
    f. 单击“确定”。

> [!NOTE]
> 在应用程序/网站上设置适用的 SPN。 用于在设置了应用程序池标识时访问应用程序。 若要使用 FQDN 名称访问 IIS 应用程序，请转到实际服务器命令提示符并键入 SetSpn 及所需参数。 例如 Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD 集成 KCD（Kerberos 委派帐户） 

转到 Kemp LoadMaster Azure AD 集成 Web 控制台 >“虚拟服务”>“管理 SSO”。

![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-12.png)

a. 导航到“服务器端单一登录配置”。

b. 在“添加新服务器端配置”中键入名称，然后单击“添加”。

c. 在“身份验证协议”中选择“Kerberos 约束委派”。

d. 在“Kerberos 领域”中键入域名。

e. 单击“设置 Kerberos 领域”。

f. 在“Kerberos 密钥分发中心”中键入域控制器 IP 地址。

g. 单击“设置 Kerberos KDC”。

h. 在“Kerberos 可信用户名”中键入 KCD 用户名。

i. 单击“设置 KDC 可信用户名”。

j. 在“Kerberos 可信用户密码”中键入密码。

k. 单击“设置 KCD 可信用户密码”。

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD 集成 ESP 

转到“虚拟服务”>“查看/修改服务”。

![Kemp LoadMaster Azure AD 集成 Web 服务器](./media/kemp-tutorial/kemp-13.png)

a. 在虚拟服务的昵称上单击“修改”。
    
b. 单击“ESP 选项”。
    
c. 在“服务器身份验证模式”下，选择“KCD”。
        
d. 在“服务器端配置”下，选择以前创建的服务器端配置文件。

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>创建 Kemp LoadMaster Azure AD 集成测试用户

在本部分，你将在 Kemp LoadMaster Azure AD 集成中创建名为 B.Simon 的用户。 在 [Kemp LoadMaster Azure AD 集成支持团队](mailto:support@kemp.ax)的配合下，将用户添加到 Kemp LoadMaster Azure AD 集成平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Kemp LoadMaster Azure AD 集成”磁贴时，应会自动登录到设置了 SSO 的 Kemp LoadMaster Azure AD 集成。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Kemp LoadMaster Azure AD 集成](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Kemp LoadMaster Azure AD 集成](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
