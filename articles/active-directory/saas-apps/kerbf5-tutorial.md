---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 F5 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 F5 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>教程：Azure Active Directory 单一登录 (SSO) 与 F5 的集成

本教程介绍如何将 F5 与 Azure Active Directory (Azure AD) 集成。 将 F5 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 F5。
* 让用户使用其 Azure AD 帐户自动登录到 F5。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

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

2. 在“引导式配置”页，单击左上角的“升级引导式配置”   。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure14.png) 

3. 在“升级引导配置”弹出屏幕上，选择“选择文件”以上传已下载的用例包，然后单击“上传和安装”按钮   。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure15.png) 

4. 升级完成后，单击“继续”按钮  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* F5 支持 **SP 和 IDP** 发起的 SSO
* 可通过三种不同的方式配置 F5 SSO。

- [为 Kerberos 应用程序配置 F5 单一登录](#configure-f5-single-sign-on-for-kerberos-application)

- [为 Header Based 应用程序配置 F5 单一登录](headerf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>密钥身份验证方案

除了新式身份验证协议（如 Open ID Connect、SAML 和 WS-Fed）的 Azure Active Directory 本机集成支持以外，F5 还为基于传统身份验证的应用扩展了使用 Azure AD 进行内部和外部访问时采用的安全访问，从而让这些应用程序可以使用新式方案，例如无密码访问。 其中包括：

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

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”和“证书(Base64)”，然后选择“下载”以下载该证书并将其保存在计算机上      。

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

- [为 Header Based 应用程序配置 F5 单一登录](headerf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>为 Kerberos 应用程序配置 F5 单一登录

### <a name="guided-configuration"></a>引导式配置

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 F5 (Kerberos) 公司站点并执行以下步骤：

1. 需要将元数据证书导入到 F5 中，该证书将在稍后的设置过程中使用。

1. 导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。 指定稍后会在配置中引用的“证书名称”  。 在“证书源”中，选择“上传文件”，指定在配置 SAML 单一登录时从 Azure 下载的证书  。 单击“导入”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure01.png) 

1. 此外，还需要 SSL 证书以获取应用程序主机名。**导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。 “导入类型”将为“PKCS 12(IIS)”   。 指定稍后会在配置中引用的“密钥名称”，并指定 PFX 文件  。 为 PFX 指定“密码”  。 单击“导入”  。

    >[!NOTE]
    >示例中的应用名称为 `Kerbapp.superdemo.live`，我们使用通配符证书，密钥名称为 `WildCard-SuperDemo.live`

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure02.png) 
 
1. 我们将使用引导式体验来设置 Azure AD 联合身份验证和应用程序访问。 转到 F5 BIG-IP 主页，选择“访问”>“引导式配置”>“联合”>“SAML 服务提供程序”   。 单击“下一步”，然后单击“下一步”以开始配置   。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure03.png) 

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure04.png)

1. 提供配置名称  。 指定“实体 ID”（与在 Azure AD 应用程序配置中配置的 ID 相同）  。 指定“主机名”  。 添加引用的说明  。 接受其余默认条目，选择并单击“保存并进行下一步”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure05.png) 

1. 在本示例中，我们将创建新的虚拟服务器，IP 为 192.168.30.200，端口为 443。 在“目标地址”中指定虚拟服务器 IP 地址  。 选择客户端“SSL 配置文件”，然后选择“新建”  。 指定之前上传的应用程序证书（在本示例中为通配符证书）和关联的密钥，然后单击“保存并进行下一步”  。

    >[!NOTE]
    >在本示例中，内部 Web 服务器在端口 80 上运行，我们希望使用端口 443 发布它。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure06.png)

1. 在“选择配置 IdP 连接器的方法”下指定“元数据”，单击“选择文件”，然后上传之前从 Azure AD 下载的元数据 XML 文件  。 为 SAML IDP 连接器指定唯一名称  。 选择之前上传的元数据签名证书  。 单击“保存并进行下一步”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure07.png)  

1. 在“选择池”下指定“新建”（或者选择已有的池）   。 其余项保持默认值。 在“池服务器”下，在“IP 地址/节点名称”下键入 IP 地址  。 指定端口  。 单击“保存并进行下一步”  。
 
    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure08.png)

1. 在“单一登录设置”屏幕上，选择“启用单一登录”  。 在“选定单一登录类型”下，选择“Kerberos”   。 将“用户名源”（此变量使用 Azure AD 中的声明映射设置）下的“session.saml.last.Identity”替换为“session.saml.last.attr.name.Identity”    。 选择“显示高级设置”  。 在“Kerberos 领域”下，键入“域名”  。 在“帐户名称/帐户密码”下，指定 APM 委派帐户和密码  。 在“KDC”字段中指定域控制器 IP  。 单击“保存并进行下一步”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure09.png)   

1. 出于本指南的目的，我们将跳过终结点检查。  有关详细信息，请参阅 F5 文档。  在屏幕上，选择“保存并进行下一步”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure10.png) 

1. 接受默认设置，然后单击“保存并进行下一步”  。 有关 SAML 会话管理设置的详细信息，请参阅 F5 文档。


    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure11.png) 
 
1. 查看摘要屏幕，然后选择“部署”以配置 BIG-IP  。
 
    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure12.png)

1. 应用程序完成配置后，单击“完成”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>高级配置

>[!NOTE]
>如需参考，请单击[此处](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>配置 Active Directory AAA 服务器

可以在 Access Policy Manager (APM) 中配置 Active Directory AAA 服务器，以指定供 APM 用于验证用户身份的域控制器和凭据。

1.  在主页选项卡上，单击“访问策略”>“AAA 服务器”>“Active Directory”  。 系统会打开 Active Directory 服务器列表屏幕。

2.  单击“创建”。  系统会打开新服务器属性屏幕。

3.  在“名称”字段中，为身份验证服务器键入唯一的名称  。

4.  在“域名”字段中，键入 Windows 域的名称  。

5.  对于“服务器连接”设置，选择以下选项之一  ：

    * 选择“使用池”，为 AAA 服务器设置高可用性  。

    * 选择“直接”，为独立功能设置 AAA 服务器  。

6.  如果选择“直接”，请在“域控制器”字段中键入名称   。

7.  如果选择“使用池”，请配置池  ：

    * 在“域控制器池名称”字段中键入名称  。

    * 通过为每个域控制器分别输入 IP 地址和主机名，然后单击“添加”按钮，来指定池中的“域控制器”   。

    * 若要监视 AAA 服务器的运行状况，可选择运行状况监视器：在本例中，仅 **gateway_icmp** 监视器适用；可从“服务器池监视器”列表中选择它  。

8.  在“管理员名称”字段中，为具有 Active Directory 管理权限的管理员键入区分大小写的名称  。 APM 使用“管理员名称”和“管理员密码”字段中的信息进行 AD 查询   。 如果对 Active Directory 进行了匿名查询配置，则无需提供管理员名称。 否则，APM 需要具有足够特权的帐户才能绑定到 Active Directory 服务器、提取用户组信息以及提取 Active Directory 密码策略用于支持与密码相关的功能。 （例如，如果在 AD 查询操作中选择“提示用户在过期之前更改密码”选项，则 APM 必须提取密码策略。）如果未在此配置中提供管理员帐户信息，则 APM 使用用户帐户来提取信息。 如果用户帐户具有足够的特权，则此方法有效。

9.  在“管理员密码”字段中，键入与域名关联的管理员密码  。

10. 在“验证管理员密码”字段中，重新键入与“域名”设置关联的管理员密码   。

11. 在“组缓存生存期”字段中，键入天数  。 默认生存期为 30 天。

12. 在“密码安全对象缓存生存期”字段中，键入天数  。 默认生存期为 30 天。

13. 从“Kerberos 预身份验证加密类型”列表中选择加密类型  。 默认值为“无”  。 如果指定加密类型，则 BIG-IP 系统将在第一个身份验证服务请求 (AS-REQ) 数据包中包括 Kerberos 预身份验证数据。

14. 在“超时”字段中，为 AAA 服务器键入超时间隔（以秒为单位）  。 （此设置可选。）

15. 单击“完成”  。 新服务器在列表中显示。 这会将新的 Active Directory 服务器添加到 Active Directory 服务器列表中。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML 配置

1. 需要将元数据证书导入到 F5 中，该证书将在稍后的设置过程中使用。 导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure18.png)

2. 若要设置 SAML IDP，请导航到“访问”>“联合”>“SAML:服务提供程序”>“外部 Idp 连接器”，然后单击“创建”>“从元数据”   。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure19.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure20.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure21.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure22.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure23.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure24.png)

1. 若要设置 SAML SP，请导航到“访问”>“联合”>“SAML 服务提供程序”>“本地 SP 服务”，然后单击“创建”   。 填写以下信息，然后单击“确定”  。

    * 类型名称：KerbApp200SAML
    * 实体 ID*： https://kerbapp200.superdemo.live
    * SP 名称设置
    * 方案：https
    * 主机：kerbapp200.superdemo.live
    * 说明：kerbapp200.superdemo.live

     ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure25.png)

     b. 选择 SP 配置 KerbApp200SAML，然后单击“绑定/取消绑定 IdP 连接器”  。

     ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure26.png)

     ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure27.png)

     c. 单击“添加新行”，并选择在上一步骤中创建的“外部 IdP 连接器”，单击“更新”，然后单击“确定”     。

     ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure28.png)

1. 若要配置 Kerberos SSO，请导航到“访问”>“单一登录”>“Kerberos”，填写信息，然后单击“完成”   。

    >[!Note]
    > 需要创建并指定 Kerberos 委托帐户。 参阅 KCD 部分（参阅附录中的“变量参考”）

    * **用户名源**：session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **用户领域源**：session.logon.last.domain

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure29.png)

1. 若要配置访问配置文件，请导航到“访问”>“配置文件/策略”>“访问配置文件(按会话策略)”，单击“创建”，填写以下信息，然后单击“完成”    。

    * 姓名：KerbApp200
    * 配置文件类型：All
    * 配置文件范围：配置文件
    * 语言：英语

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure30.png)

1. 单击名称“KerbApp200”，填写以下信息，然后单击“更新”  。

    * 域 Cookie：superdemo.live
    * SSO 配置：KerAppSSO_sso

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure31.png)

1. 单击“访问策略”，然后单击配置文件“KerbApp200”的“编辑访问策略”   。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure32.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure33.png)

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure36.png)

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. 若要添加新节点，请导航到“本地流量”>“节点”>“节点列表”，单击“创建”，填写以下信息，然后单击“完成”   。

    * 姓名：KerbApp200
    * 说明:KerbApp200
    * 地址：192.168.20.200

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure39.png)

1. 若要创建新池，请导航到“本地流量”>“池”>“池列表”，单击“创建”，填写以下信息，然后单击“完成”   。

    * 姓名：KerbApp200-Pool
    * 说明:KerbApp200-Pool
    * 运行状况监视器：http
    * 地址：192.168.20.200
    * 服务端口：81

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure40.png)

1. 若要创建虚拟服务器，请导航到“本地流量”>“虚拟服务器”>“虚拟服务器列表”>“+”，填写以下信息，然后单击“完成”   。

    * 姓名：KerbApp200
    * 目标地址/掩码：Host 192.168.30.200
    * 服务端口：Port 443 HTTPS
    * 访问配置文件：KerbApp200
    * 指定在上一步骤中创建的访问配置文件

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure41.png)

        ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>设置 Kerberos 委托 

>[!NOTE]
>如需参考，请单击[此处](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **步骤 1：** 创建委派帐户

    **示例：**
    * 域名：**superdemo.live**

    * Sam 帐户名：**big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **步骤 2：** 设置 SPN（在 APM 委派帐户中）

    **示例：**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **步骤 3：** SPN 委派（用于应用服务帐户）为 F5 委派帐户设置适当的委派。
    以下示例为 FRP-App1.superdemo.live app 配置 KCD 的 APM 委派帐户 。

    ![F5 (Kerberos) 配置](./media/kerbf5-tutorial/configure43.png)

* 在[此处](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)提供上述参考文档中所述的详细信息。

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

- [为 Header Based 应用程序配置 F5 单一登录](headerf5-tutorial.md)

- [为 Advanced Kerberos 应用程序配置 F5 单一登录](advance-kerbf5-tutorial.md)

