---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 F5 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 F5 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad6b7150a43a286a4bec39a0482e08f50d95c06
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>教程：Azure Active Directory 单一登录 (SSO) 与 F5 的集成

本教程介绍如何将 F5 与 Azure Active Directory (Azure AD) 集成。 将 F5 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 F5。
* 让用户使用其 Azure AD 帐户自动登录到 F5。
* 在一个中心位置（Azure 门户）管理帐户。

若要详细了解 SaaS 应用与 Azure AD 单一登录的集成，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

* 已启用 F5 单一登录 (SSO) 的订阅。

* 部署联合解决方案需要以下许可证：

    * F5 BIG-IP® Best 捆绑包（或者） 

    * F5 BIG-IP Access Policy Manager™ (APM) 独立许可证 

    * 现有的 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 加载项许可证。

    * 除了上述许可证以外，F5 系统还可通过以下方式获得许可： 

        * URL 筛选订阅，用于使用 URL 类别数据库

        * F5 IP 智能订阅，用于检测和阻止已知攻击者和恶意流量
     
        * 网络硬件安全模块 (HSM)，用于保护和管理强身份验证使用的数字密钥

* F5 BIG-IP 系统预配了 APM 模块（LTM 是可选的）

* 尽管是可选的，但我们强烈建议在[同步/故障转移设备组](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) 中部署 F5 系统，其中包括活动备用对，并具有针对高可用性 (HA) 的浮动 IP 地址。 可以使用链路聚合控制协议 (LACP) 实现进一步的接口冗余。 LACP 将连接的物理接口作为单个虚拟接口（聚合组）管理，并检测组内的任何接口故障。

* Kerberos 应用程序使用本地 AD 服务帐户进行约束委派。  请参阅 [F5 文档](https://support.f5.com/csp/article/K43063049)，了解如何创建 AD 委派帐户。

## <a name="access-guided-configuration"></a>访问引导式配置

* 13.1.0.8 及更高版本的 F5 TMOS 支持访问引导式配置。 如果 BIG-IP 系统运行的版本低于 13.1.0.8，请参阅“高级配置”部分  。

* 访问引导式配置提供全新且简化的用户体验。 此体系结构基于工作流，提供针对选定拓扑定制的直观、可重入的配置步骤。

* 在继续配置之前，请从 [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) 下载最新用例包，升级引导式配置。 若要升级，请按照以下流程操作。

    >[!NOTE]
    >下面的屏幕截图适用于最新的已发布版本（AGC 版本为 5.0 的 BIG-IP 15.0）。 对于 13.1.0.8 版到 BIG-IP 最新版本，以下配置步骤对此用例有效。

1. 在 F5 BIG-IP Web UI 上，单击“访问”>>“引导式配置”  。

1. 在“引导式配置”页，单击左上角的“升级引导式配置”   。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure14.png) 

1. 在“升级引导配置”弹出屏幕上，选择“选择文件”以上传已下载的用例包，然后单击“上传和安装”按钮   。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure15.png) 

1. 升级完成后，单击“继续”按钮  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* 可通过三种不同的方式配置 F5 SSO。

- [为 Header Based 应用程序配置 F5 单一登录](#configure-f5-single-sign-on-for-header-based-application)

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>密钥身份验证方案

* 除了新式身份验证协议（如 Open ID Connect、SAML 和 WS-Fed）的 Azure Active Directory 本机集成支持以外，F5 还为基于传统身份验证的应用扩展了使用 Azure AD 进行内部和外部访问时采用的安全访问，从而让这些应用程序可以使用新式方案，例如无密码访问。 其中包括：

* 基于标头的身份验证应用

* Kerberos 身份验证应用

* 匿名身份验证或非内置身份验证应用

* NTLM 身份验证应用（通过针对用户的双重提示进行保护）

* 基于窗体的身份验证应用（通过针对用户的双重提示进行保护）

## <a name="adding-f5-from-the-gallery"></a>从库中添加 F5

若要配置 F5 与 Azure AD 的集成，需要从库中将 F5 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **F5**。 
1. 在结果面板中选择“F5”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>配置并测试 F5 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 F5 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 F5 中的相关用户之间建立链接关系。

若要配置并测试 F5 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 F5 SSO](#configure-f5-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 F5 测试用户](#create-f5-test-user)** - 在 F5 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“F5”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [F5 客户端支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”和“证书(Base64)”，选择“下载”以下载该证书并将其保存在计算机上      。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 F5”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 F5 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“F5”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  
1. 单击“条件访问”  。
1. 单击“新建策略”  。
1. 现在，可将 F5 应用视为 CA 策略的资源，并应用任何条件访问，包括多重身份验证、基于设备的访问控制或标识保护策略。

## <a name="configure-f5-sso"></a>配置 F5 SSO

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>为 Header Based 应用程序配置 F5 单一登录

### <a name="guided-configuration"></a>引导式配置

1. 打开新的 Web 浏览器窗口，以管理员身份登录 F5 (Header Based) 公司站点，并执行以下步骤：

1. 导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。 指定稍后会在配置中引用的“证书名称”  。 在“证书源”中，选择“上传文件”，指定在配置 SAML 单一登录时从 Azure 下载的证书  。 单击“导入”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure12.png)
 
1. 此外，还需要 **SSL 证书以获取应用程序主机名。导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”** 。 在右下角选择“导入”  。 “导入类型”将为“PKCS 12(IIS)”   。 指定稍后会在配置中引用的“密钥名称”，并指定 PFX 文件  。 为 PFX 指定“密码”  。 单击“导入”  。

    >[!NOTE]
    >在此示例中，我们的应用名称是 `Headerapp.superdemo.live`，我们使用通配符证书，我们的密钥名称为 `WildCard-SuperDemo.live`。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure13.png)

1. 我们将使用引导式体验来设置 Azure AD 联合身份验证和应用程序访问。 转到 F5 BIG-IP 主页，选择“访问”>“引导式配置”>“联合”>“SAML 服务提供程序”   。 单击“下一步”，然后单击“下一步”以开始配置   。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure01.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure02.png)
 
1. 提供配置名称  。 指定“实体 ID”（与在 Azure AD 应用程序配置中配置的 ID 相同）  。 指定“主机名”  。 添加引用的说明  。 接受其余默认条目，选择并单击“保存并进行下一步”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure03.png) 

1. 在本示例中，我们将新建一个虚拟服务，IP 为 192.168.30.20，端口 443。 在“目标地址”中指定虚拟服务器 IP 地址  。 选择客户端“SSL 配置文件”，然后选择“新建”  。 指定之前上传的应用程序证书（在本示例中为通配符证书）和关联的密钥，然后单击“保存并进行下一步”  。

    >[!NOTE]
    >在此示例中，我们的内部 Web 服务器在端口 888 上运行，我们希望使用端口 443 发布它。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure04.png) 

1. 在“选择配置 IdP 连接器的方法”下指定“元数据”，单击“选择文件”，然后上传之前从 Azure AD 下载的元数据 XML 文件  。 为 SAML IDP 连接器指定唯一名称  。 选择之前上传的元数据签名证书  。 单击“保存并进行下一步”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure05.png)
 
1. 在“选择池”下指定“新建”（或者选择已有的池）   。 其余项保持默认值。 在“池服务器”下，在“IP 地址/节点名称”下键入 IP 地址  。 指定端口  。 单击“保存并进行下一步”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure06.png)

1. 在“单一登录设置”屏幕上，选择“启用单一登录”  。 在选择的单一登录类型下，选择“基于 HTTP 标头”  。 在“用户名源”下使用 session.saml.last.attr.name.Identity（此变量是使用 Azure AD 中的声明映射设置的）替代 session.saml.last.Identity   。 在 SSO 标头下。

    * HeaderName：  MyAuthorization

    * 标头值：%{session.saml.last.attr.name.Identity} 

    * 单击“保存并进行下一步” 

    有关变量和值的完整列表，请参阅附录。 如果需要，可以添加更多标头。

    >[!NOTE]
    >帐户名是创建的 F5 委托帐户（请查看 F5 文档）。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure07.png) 

1. 出于本指南的目的，我们将跳过终结点检查。  有关详细信息，请参阅 F5 文档。 选择“保存并进行下一步”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure08.png)

1. 接受默认设置，然后单击“保存并进行下一步”  。 有关 SAML 会话管理设置的详细信息，请参阅 F5 文档。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure09.png)

1. 查看摘要屏幕，然后选择“部署”以配置 BIG-IP  。 单击“完成”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure10.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>高级配置

该部分针对的是无法使用引导式配置或希望添加/修改其他参数的情况。 需要 SSL 证书以获取应用程序主机名。

1. 导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。 “导入类型”将为“PKCS 12(IIS)”   。 指定稍后会在配置中引用的“密钥名称”，并指定 PFX 文件  。 为 PFX 指定“密码”  。 单击“导入”  。

    >[!NOTE]
    >在此示例中，我们的应用名称是 `Headerapp.superdemo.live`，我们使用通配符证书，我们的密钥名称为 `WildCard-SuperDemo.live`。
  
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>向 BigIP-F5 添加新的 Web 服务器

1. 单击“主页”>“IApps”>“应用程序服务”>“应用程序”>“创建”  。

1. 提供名称，并在“模板”下选择“f5.http”    。
 
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure18.png)

1. 在这种情况下，我们会在外部将 HeaderApp2 作为 HTTPS 发布。在“BIG-IP 系统应如何处理 SSL 流量?”下我们指定“终止来自客户端的 SSL、发往服务器的纯文本(SSL 卸载)”   。 在“希望使用哪个 SSL 证书?”和“希望使用哪个 SSL 私钥?”下选择自己的证书和密钥  。 在“希望虚拟服务器采用什么 IP 地址?”下指定虚拟服务器 IP  。 

    * 指定其他详细信息 

        * FQDN  

        * 指定现有应用池或创建新应用池。

        * 如果要创建新的应用服务器，请指定内部 IP 地址和端口号   。

        ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure19.png) 

1. 单击“完成”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure20.png) 

1. 确保可以修改应用属性。 单击“主页”>“IApps”>“应用程序服务”：  “应用程序”>>“HeaderApp2”。 取消选中“严格更新”（我们将修改 GUI 之外的部分设置）  。 单击“更新”按钮  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure21.png) 

1. 此时，应该能够浏览虚拟服务器。

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>将 F5 配置为 SP，将 Azure 配置为 IDP

1.  单击“访问”>“联合”>“SAML 服务提供程序”>“本地 SP 服务”，单击“创建”或 + 号  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure22.png)

1. 指定服务提供程序服务的详细信息。 指定表示 F5 SP 配置的“名称”  。 指定“实体 ID”，通常与应用程序 URL 相同  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure23.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure24.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure25.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure26.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure27.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>创建 Idp 连接器

1. 单击“绑定/取消绑定 IdP 连接器”按钮，选择“新建 IdP 连接器”，并在“元数据”选项中进行选择，然后执行以下步骤    ：
 
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure29.png)

    a. 浏览到从 Azure AD 下载的 metadata.xml 文件，并指定标识提供程序名称  。

    b. 单击“确定”  。

    c. 创建连接器后，将自动从元数据 xml 文件中准备好证书。
    
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure30.png)

    d. 配置 F5BIG-IP 以将所有请求发送到 Azure AD。

    e. 单击“添加新行”，选择“AzureIDP”（如前面的步骤中创建的那样）   ，指定 

    f. 匹配源   =  %{session.server.landinguri}  

    g. 匹配值     = /  *

    h. 单击“更新” 

    i. 单击 **“确定”**

    j. SAML IDP 设置已完成 
    
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>配置 F5 策略以将用户重定向到 Azure SAML IDP

1. 为了配置 F5 策略以将用户重定向到 Azure SAML IDP，请执行以下步骤：

    a. 单击“主页”>“访问”>“配置文件/策略”>“访问配置文件”  。

    b. 单击“创建”按钮。 

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure32.png)
 
    c. 指定名称，本例中为 HeaderAppAzureSAMLPolicy  。

    d. 可以自定义其他设置，请参阅 F5 文档。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure33.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure34.png) 

    e. 单击“完成”  。

    f. 策略创建完成后，单击策略，并转到“访问策略”选项卡  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure35.png)
 
    g. 单击“Visual Policy”编辑器，编辑“配置文件的访问策略”链接   。

    h. 单击“Visual Policy”编辑器中的 + 号并选择“SAML 身份验证”  。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure36.png)

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure37.png)
 
    i. 单击“添加项”  。

    j. 在“属性”下指定名称，在“AAA 服务器”下选择之前配置的 SP，单击“保存”     。
 
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure38.png)

    k. 基本策略已准备就绪，可以自定义策略以合并其他源/属性存储。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure39.png)
 
    l. 请务必单击顶部的“应用访问策略”链接  。

### <a name="apply-access-profile-to-the-virtual-server"></a>将访问配置文件应用到虚拟服务器

1. 将访问配置文件分配给虚拟服务器，以便 F5 BIG-IP APM 将配置文件设置应用于传入流量，并运行之前定义的访问策略。

    a. 单击“主页” > “本地流量” > “虚拟服务器”    。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure40.png)
 
    b. 单击虚拟服务器，滚动至“访问策略”部分的“访问配置文件”下拉菜单，并选择已创建的 SAML 策略，本例中为 HeaderAppAzureSAMLPolicy  

    c. 单击“更新” 
 
    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure41.png)

    d. 创建一个 F5 BIG-IP iRule®，以便从传入断言中提取自定义 SAML 属性，并将其作为 HTTP 标头传递到后端测试应用程序。 单击“主页”>“本地流量”>“iRules”>“iRule 列表”> 单击“创建” 

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure42.png)
 
    e. 将下面的 F5 BIG-IP iRule 文本粘贴到定义窗口中。

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    下面是示例输出 

    ![F5 (Header Based) 配置](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>创建 F5 测试用户

在本部分，你将在 F5 中创建名为 B.Simon 的用户。 在  [F5 支持团队](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)的配合下，将用户添加到 F5 平台。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“F5”磁贴时，应会自动登录到设置了 SSO 的 F5。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 F5](https://aad.portal.azure.com/)

- [为 Kerberos 应用程序配置 F5 单一登录](kerbf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

