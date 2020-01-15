---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Citrix NetScaler（基于 Kerberos 的身份验证）集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Citrix NetScaler 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431350"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Citrix NetScaler（基于 Kerberos 的身份验证）集成

本教程介绍如何将 Citrix NetScaler 与 Azure Active Directory (Azure AD) 集成。 将 Citrix NetScaler 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Citrix NetScaler。
* 让用户使用其 Azure AD 帐户自动登录到 Citrix NetScaler。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Citrix NetScaler 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Citrix NetScaler 支持 SP 发起的 SSO 

* Citrix NetScaler 支持**实时**用户预配

- [为基于 Kerberos 的身份验证配置 Citrix NetScaler 单一登录](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [为基于标头的身份验证配置 Citrix NetScaler 单一登录](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>从库中添加 Citrix NetScaler

若要配置 Citrix NetScaler 与 Azure AD 的集成，需要从库中将 Citrix NetScaler 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Citrix NetScaler   。
1. 从结果面板中选择 Citrix NetScaler，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>为 Citrix NetScaler 配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Citrix NetScaler 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Citrix NetScaler 中的相关用户之间建立关联关系。

若要配置并测试 Citrix NetScaler 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Citrix NetScaler SSO](#configure-citrix-netscaler-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Citrix NetScaler 测试用户](#create-citrix-netscaler-test-user)** - 在 Citrix NetScaler 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的 Citrix NetScaler 应用程序集成页上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<<Your FQDN>>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Citrix NetScaler 客户端支持团队](https://www.citrix.com/contact/technical-support.html)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

    > [!NOTE]
    > 要让 SSO 正常运行，这些 URL 应可通过公共网站进行访问。 需要在 Netscaler 端启用防火墙或其他安全设置，才能让 Azure AD 在所配置的 ACS URL 上发布令牌。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，查找“应用联合元数据 URL”，然后复制 URL 并将其保存在记事本上    。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Citrix NetScaler”部分中，根据要求复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 Citrix NetScaler 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Citrix NetScaler”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-citrix-netscaler-sso"></a>配置 Citrix NetScaler SSO

- [为基于 Kerberos 的身份验证配置 Citrix NetScaler 单一登录](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [为基于标头的身份验证配置 Citrix NetScaler 单一登录](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>发布 Web 服务器 

1. 创建“虚拟服务器”  。

    a. 转到“流量管理 > 负载均衡 > 服务”  。
    
    b. 单击“添加”  。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/web01.png)

    c. 指定运行以下应用程序的 Web 服务器的详细信息：
    * **服务名称**
    * 服务器 IP/现有服务器 
    * 协议 
    * 端口 

     ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>配置负载均衡器

1. 若要配置负载均衡器，请执行以下步骤：

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/load01.png)

    a. 转到“流量管理 > 负载均衡 > 虚拟服务器”  。

    b. 单击“添加”  。

    c. 指定以下详细信息：

    * **名称**
    * 协议 
    * **IP 地址**
    * 端口 
    * 单击“确定” 

### <a name="bind-virtual-server"></a>绑定虚拟服务器

将负载均衡器与刚创建的虚拟服务器绑定。

![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>绑定证书

由于此服务将以 SSL 形式发布，因此请绑定服务器证书，然后测试应用程序。

![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML 配置文件

### <a name="create-authentication-policy"></a>创建身份验证策略

1. 转到“安全性 > AAA - 应用程序流量 > 策略 > 身份验证 > 身份验证策略”  。

2. 单击“添加”，然后指定详细信息  。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/policy01.png)

    a. 为“身份验证策略”命名  。

    b. 表达式：true  。

    c. 操作类型 SAML  。

    d. 操作 = 单击“添加”（遵循“创建身份验证 SAML 服务器向导”）  。
    
    e. 单击“身份验证策略”上的“创建”  。

### <a name="create-authentication-saml-server"></a>创建身份验证 SAML 服务器

1. 执行以下步骤：

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/server01.png)

    a. 指定“名称”  。

    b. 导入元数据（在 Azure SAML UI 中指定从上面复制的联合元数据 URL）。
    
    c. 指定“颁发者名称”  。

    d. 单击“创建”  。

### <a name="create-authentication-virtual-server"></a>创建身份验证虚拟服务器

1.  转到“安全性 > AAA - 应用程序流量 >> 身份验证虚拟服务器”  。

2.  单击“添加”并执行以下步骤  ：

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/server02.png)

    a.  提供“名称”  。

    b.  选择“非寻址”  。

    c.  协议 SSL  。

    d.  单击“确定”。 

    e.  单击 **“继续”** 。

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>配置身份验证虚拟服务器以使用 Azure AD

需要修改身份验证虚拟服务器的 2 个部分。

1.  **高级身份验证策略**

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/virtual01.png)

    a. 选择此前创建的“身份验证策略”  。

    b. 单击“绑定”  。

      ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/virtual02.png)

2. **基于窗体的虚拟服务器**

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  需要提供 FQDN，这是因为它由 UI 强制执行  。

    b.  选择要通过 Azure AD 身份验证进行保护的“虚拟服务器负载均衡器”  。

    c.  单击“绑定”  。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >另请确保单击“身份验证虚拟服务器配置”页上的“完成”  。

3. 验证更改。 浏览应用程序 URL。 此时应显示租户登录页，而不是之前未经身份验证的访问。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>为基于 Kerberos 的身份验证配置 Citrix NetScaler 单一登录

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>为 Citrix ADC 创建 Kerberos 委托帐户

1. 创建用户帐户（在此示例中为 AppDelegation）。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos01.png)

2. 在此帐户上设置一个主机 SPN。

    * setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation
    
        在以上示例中

        a. Identt.work    （域 FQDN）

        b. Identt        （域 Netbios 名称）

        c. AppDelegation（委托用户帐户名称）

3. 为 WebServer 配置委托 
 
    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >在上面的示例中，运行 WIA 站点的内部 Web 服务器名称为 cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD（Kerberos 委托帐户）

1.  转到“Citrix 网关”>“AAA KCD (Kerberos 约束委托)帐户”。 

2.  单击“添加”，指定以下详细信息：

    a.  指定“名称”  。

    b.  **领域**。

    c.  **服务 SPN**  `http/<host/fqdn>@DOMAIN.COM`。
    
    >[!NOTE]
    >@DOMAIN.com 是必需的，采用大写。

    d.  指定“委托用户帐户”  。

    e.  勾选“委托用户的密码”并指定“密码”。 

    f.  单击“确定”。 
 
    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix 流量策略和流量配置文件

1.  转到“安全性 > AAA - 应用程序流量 > 策略 > 流量策略、配置文件和窗体 SSO 配置文件流量策略”。 

2.  选择“流量配置文件”。 

3.  单击“添加”  。

4.  配置“流量配置文件”。

    a.  指定“名称”  。

    b.  指定“单一登录”  。

    c.  从下拉列表中指定在此前的步骤中创建的 **KCD 帐户**。

    d.  单击“确定”。 

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  选择“流量策略”。 

6.  单击“添加”  。

7.  配置“流量策略”。

    a.  指定“名称”  。

    b.  从下拉列表中选择此前创建的**流量配置文件**。

    c.  将表达式设置为 **true**。

    d.  单击“确定”  。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Citrix 将流量策略绑定到虚拟服务器

使用 GUI 将流量策略绑定到特定虚拟服务器。

* 导航到“流量管理 > 负载均衡 > 虚拟服务器”  。

* 在“虚拟服务器”的详细信息窗格列表中，选择要绑定重写策略的虚拟服务器，然后单击“打开”   。

* 在“配置虚拟服务器(负载均衡)”对话框中，选择“策略”选项卡  。NetScaler 上配置的所有策略都显示在列表中。
 
    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  选择要绑定到此虚拟服务器的策略名称旁的“复选框”  。
 
    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos09.png)

1. 只有在绑定策略后，才单击“完成”。 
 
    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos10.png)

1. 使用 Windows 集成网站进行测试。

    ![Citrix NetScaler 配置](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>创建 Citrix NetScaler 测试用户

在本部分，我们在 Citrix NetScaler 中创建一个名为 B.Simon 的用户。 Citrix NetScaler 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Citrix NetScaler 中尚不存在用户，则会在身份验证后创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需要联系 [Citrix NetScaler 客户端支持团队](https://www.citrix.com/contact/technical-support.html)。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Citrix NetScaler 磁贴时，应当会自动登录到设置了 SSO 的 Citrix NetScaler。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Citrix NetScaler](https://aad.portal.azure.com/)

- [为基于标头的身份验证配置 Citrix NetScaler 单一登录](header-citrix-netscaler-tutorial.md)
