---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Oracle PeopleSoft - Protected by F5 BIG-IP APM 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Oracle PeopleSoft - Protected by F5 BIG-IP APM 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 7d2dc1d5d02f1a371d6d94f9eeddf395d49126d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620131"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Oracle PeopleSoft - Protected by F5 BIG-IP APM 集成

在本教程中，你将学习如何将 Oracle PeopleSoft - Protected by F5 BIG-IP APM 与 Azure Active Directory (Azure AD) 集成。 将 Azure AD 与 Oracle PeopleSoft - Protected by F5 BIG-IP APM 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Oracle PeopleSoft - Protected by F5 BIG-IP APM。
* 让用户能够使用其 Azure AD 帐户自动登录到 Oracle PeopleSoft - Protected by F5 BIG-IP APM。
* 在一个中心位置（Azure 门户）管理帐户。
* 本教程介绍 Oracle PeopleSoft ELM 的说明。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

1. 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
1. 启用了 Oracle PeopleSoft - Protected by F5 BIG-IP APM 单一登录 (SSO) 的订阅。

1. 部署联合解决方案需要以下许可证：

    1. F5 BIG-IP® Best 捆绑包（或者） 
    2. F5 BIG-IP Access Policy Manager™ (APM) 独立许可证 
    3. 现有的 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 加载项许可证。
    4. 除了上述许可证以外，F5 系统还可通过以下方式获得许可： 
        * URL 筛选订阅，用于使用 URL 类别数据库 
        * F5 IP 智能订阅，用于检测和阻止已知攻击者和恶意流量 
        * 网络硬件安全模块 (HSM)，用于保护和管理强身份验证使用的数字密钥
1. F5 BIG-IP 系统预配了 APM 模块（LTM 是可选的） 
1. 尽管是可选的，但我们强烈建议在[同步/故障转移设备组](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) 中部署 F5 系统，其中包括活动备用对，并具有针对高可用性 (HA) 的浮动 IP 地址。 可以使用链路聚合控制协议 (LACP) 实现进一步的接口冗余。 LACP 将连接的物理接口作为单个虚拟接口（聚合组）管理，并检测组内的任何接口故障。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Oracle PeopleSoft - Protected by F5 BIG-IP APM 支持 SP 和 IDP 发起的 SSO

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>从库中添加 Oracle PeopleSoft - Protected by F5 BIG-IP APM

若要配置 Oracle PeopleSoft - Protected by F5 BIG-IP APM 与 Azure AD 的集成，需要从库中将 Oracle PeopleSoft - Protected by F5 BIG-IP APM 添加到托管 SaaS 应用的列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中的搜索框中，键入“Oracle PeopleSoft - Protected by F5 BIG-IP APM” 。
1. 从结果面板中选择“Oracle PeopleSoft - Protected by F5 BIG-IP APM”，然后添加应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>配置和测试适用于 Oracle PeopleSoft - Protected by F5 BIG-IP APM 的 Azure AD SSO

使用名为 B.Simon 的测试用户为 Oracle PeopleSoft - Protected by F5 BIG-IP APM 配置和测试 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Oracle PeopleSoft - Protected by F5 BIG-IP APM 相关用户之间建立关联。

若要使用 Oracle PeopleSoft - Protected by F5 BIG-IP APM 配置和测试 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Oracle PeopleSoft - Protected by F5 BIG-IP APM](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Oracle PeopleSoft - Protected by F5 BIG-IP APM 测试用户](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** - 在 Oracle PeopleSoft - Protected by F5 BIG-IP APM 中拥有 B.Simon 的对应项，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Oracle PeopleSoft - Protected by F5 BIG-IP APM”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<FQDN>.peoplesoft.f5.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. 在“注销 URL”文本框中，使用以下模式键入 URL：`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >这些不是实际值。 请使用实际的登录 URL、标识符、回复 URL 和注销 URL 更新这些值。 若要获取此值，请联系 [Oracle PeopleSoft - Protected by F5 BIG-IP APM 支持团队](https://support.f5.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. Oracle PeopleSoft - Protected by F5 BIG-IP APM 应用程序需要特定格式的 SAML 断言，因此需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性，Oracle PeopleSoft - Protected by F5 BIG-IP APM 应用程序还要求在 SAML 响应中再传递回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. 在“设置 SAML 单一登录”页面的“SAML 签名证书”部分中，下载联合元数据 XML 和证书 (Base64)，并将其保存在计算机上   。

    ![证书下载链接](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将授予 B.Simon 访问 Oracle PeopleSoft - Protected by F5 BIG-IP APM 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Oracle PeopleSoft - Protected by F5 BIG-IP APM”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>配置 Oracle PeopleSoft-Protected by F5 BIG-IP APM SSO

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP 配置

将元数据证书导入到 F5 中，该证书稍后将在设置过程中使用。 导航到“系统”>“证书管理”>“流量证书管理”>“SSL 证书列表”  。 在右下角选择“导入”  。

![F5 SAML SP 配置](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>设置 SAML IDP 连接器 

1. 导航到“访问”>“联合”>“SAML:服务提供程序”>“外部 Idp 连接器”，然后单击“创建”>“从元数据”。

    ![F5 SAML IDP 连接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. 在下列页面中，单击“浏览”以上传 xml 文件。

1. 在“标识提供程序名称”文本框中提供有效名称，然后单击“确定” 。

    ![新建 SAML IDP 连接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. 在“安全性设置”选项卡中执行所需的步骤，然后单击“确定” 。

    ![编辑 SAML IDP 连接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>设置 SAML SP

1. 导航到“访问”>“联合”>“SAML 服务提供程序”>“本地 SP 服务”，然后单击“创建” 。 填写以下信息，然后单击“确定”。

    * 名称：`<Name>`
    * 实体 ID：`https://<FQDN>.peoplesoft.f5.com`
    * SP 名称设置
        * 方案：`https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * 说明：`<Description>`

    ![新建 SAML SP 服务](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. 选择 SP 配置 PeopleSoftAppSSO，然后单击“绑定/取消绑定 IdP 连接器”。
单击“添加新行”，并选择在上一步骤中创建的“外部 IdP 连接器”，单击“更新”，然后单击“确定”   。

    ![创建 SAML SP 服务](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>配置应用程序

### <a name="create-a-new-pool"></a>创建新池
1. 导航到“本地流量”>“池”>“池列表”，单击“创建”，填写以下信息，然后单击“完成”  。

    * 名称：`<Name>`
    * 说明：`<Description>`
    * 运行状况监视器：`http`
    * 地址：`<Address>`
    * 服务端口：`<Service Port>`

    ![创建新池](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>创建新的客户端 SSL 配置文件

导航到“本地流量”>“配置文件”>“SSL”>“客户端”>“+”，填写以下信息，然后单击“完成” 。

* 名称：`<Name>`
* 证书：`<Certificate>`
* 键：`<Key>`

![新建客户端 SSL 配置文件](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>创建新的虚拟服务器

1. 导航到“本地流量”>“虚拟服务器”>“虚拟服务器列表”>“+”，填写以下信息，然后单击“完成” 。
    * 名称：`<Name>`
    * 目标地址/掩码：`<Address>`
    * 服务端口：Port 443 HTTPS
    * HTTP 配置文件（客户端）：http

    ![创建新的虚拟服务器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. 在下列页面中填写以下值：

    * SSL 配置文件（客户端）：`<SSL Profile>`
    * 源地址转换：自动映射
    * 访问配置文件：`<Access Profile>`
    * 默认池：`<Pool>`


    ![创建新的虚拟服务器 peoplesoft 池](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>设置 PeopleSoft 应用程序，支持将 F5 Big-IP APM 用作单一登录解决方案

>[!Note]
> 请参考 https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. 使用管理员凭据登录到 Peoplesoft 控制台 `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start`（凭据示例：PS/PS）

    ![管理员自助服务](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. 在 PeopleSoft 应用程序中，创建“OAMPSFT”作为新的用户配置文件，并关联 PeopleSoft 用户等安全性较低的角色 。
导航到“Peopletools”>“安全性”>“用户配置文件”>“用户配置文件”，创建新的用户配置文件，例如OAMPSFT，然后添加“Peoplesoft 用户” 。

    ![PeopleSoft 用户](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. 访问 Web 配置文件，并输入“OAMPSFT”作为公共访问用户 ID 。

    ![用户配置文件](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. 从 PeopleTools 应用程序设计器中，打开 FUNCLIB_LDAP 记录 。

    ![Web 配置文件配置](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. 更新用户标头，使其具有 OAMSSO_AUTHENTICATION 函数的 PS_SSO_UID 。
在 getWWWAuthConfig() 函数中，使用在 Web 配置文件中定义的 OAMPSFT 来替换分配给 &defaultUserId 的值  。 保存记录定义。

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. 访问“登录 PeopleCode”页面（PeopleTools、安全性、安全性对象、登录 PeopleCode），并启用 OAMSSO_AUTHENTICATION 函数 - 适用于 Oracle Access Manager 单一登录的登录 PeopleCode 。

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>设置 F5 Big-IP APM，使用 PeopleSoft 用户 ID 填充“PS_SSO_UID”HTTP 标头

### <a name="configuring-per-request-policy"></a>配置按请求策略
1. 导航到“访问”>“配置文件/策略”>“按请求策略”，单击“创建”，填写以下信息，然后单击“完成”  。

    * 名称：`<Name>`
    * 配置文件类型：All
    * 语言：`<Language>`

    ![配置按请求策略 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. 单击“编辑”来编辑按请求策略 `<Name>` ![编辑按请求策略 PeopleSoftSSO](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>将按请求策略分配给虚拟服务器

导航到“本地流量”>“虚拟服务器”>“虚拟服务器列表”>“PeopleSoftApp”，指定 `<Name>` 作为按请求策略

![将 PeopleSoftSSO 用作按请求策略 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>设置 F5 Big-IP APM，支持从 PeopleSoft 应用程序单一注销

若要为所有 PeopleSoft 用户添加单一注销支持，请执行以下步骤：

1. 确定适合 PeopleSoft 门户的正确的注销 URL
    * 若要确定 PeopleSoft 应用程序用来结束用户会话的地址，需要使用任意 Web 浏览器打开门户，并启用浏览器调试工具，如下例所示：

        ![PeopleSoft 门户的注销 URL ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * 找到具有 `PT_LOGOUT_MENU` ID 的元素，使用查询参数保存 URL 路径。 在我们的示例中，得到了以下值：`/psp/ps/?cmd=logout`

1. 创建 LTM iRule，用于将用户重定向到 APM 注销 URL：`/my.logout.php3`

    * 导航到“本地流量”>“iRule”，单击“创建”，填写以下信息，然后单击“完成”  。

        名称：`<Name>`  
        定义：  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. 将创建的 iRule 分配给虚拟服务器

    * 导航到“本地流量”>“虚拟服务器”>“虚拟服务器列表”>“PeopleSoftApp”>“资源”。 单击“管理…” 按钮：   

    * 指定 `<Name>` 作为已启用的 iRule，然后单击“完成”

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * 将“名称”文本框值设为 `<Name>` 

        ![_iRule_PeopleSoftApp 已完成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>创建 Oracle PeopleSoft-Protected by F5 BIG-IP APM 测试用户

在本部分中，你要在 Oracle PeopleSoft-Protected by F5 BIG-IP APM 中创建一个名为 B.Simon 的用户。 与 [Oracle PeopleSoft-Protected by F5 BIG-IP APM 支持团队](https://support.f5.com)合作，在 Oracle PeopleSoft-Protected by F5 BIG-IP APM 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 Oracle PeopleSoft-Protected by F5 BIG-IP APM 登录 URL，你可在这里启动登录流。  

* 直接转到 Oracle PeopleSoft-Protected by F5 BIG-IP APM 登录 URL，从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Oracle PeopleSoft-Protected by F5 BIG-IP APM 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 点击访问面板中的 Oracle PeopleSoft-Protected by F5 BIG-IP APM 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Oracle PeopleSoft-Protected by F5 BIG-IP APM。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 Oracle PeopleSoft-Protected by F5 BIG-IP APM 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
