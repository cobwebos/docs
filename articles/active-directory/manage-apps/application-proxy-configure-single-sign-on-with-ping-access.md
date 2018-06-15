---
title: 使用 PingAccess for Azure AD 应用程序代理进行基于标头的身份验证 | Microsoft Docs
description: 使用 PingAccess 和应用代理发布应用程序，以支持基于标头的身份验证。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 37b5e5d05559088d7f5aac796f452f2ed83447fc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294625"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录

将 Azure Active Directory 应用程序代理与 PingAccess 配合使用可让 Azure Active Directory 客户访问更多的应用程序。 PingAccess 扩充了[现有的应用程序代理产品/服务](application-proxy.md)，即，增加了对使用标头进行身份验证的应用程序的单一登录访问功能。

## <a name="what-is-pingaccess-for-azure-ad"></a>什么是 PingAccess for Azure AD？

PingAccess for Azure Active Directory 是一种 PingAccess 产品/服务，能够允许用户访问和单一登录使用标头进行身份验证的应用程序。 应用程序代理将这些应用视为与其他任何应用一样，它使用 Azure AD 对访问进行身份验证，然后通过连接器服务传递流量。 PingAccess 驻留在应用的前面，可将 Azure AD 提供的访问令牌转换为标头，使应用程序能够接收采用可读格式的身份验证令牌。

用户登录后使用企业应用时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。 

由于应用程序代理连接器会将远程流量定向到所有应用（不管其身份验证类型为何），因此也会继续自动进行负载均衡。

## <a name="how-do-i-get-access"></a>如何获取访问权限？

由于本方案是通过 Azure Active Directory 与 PingAccess 之间的合作提供的，因此需要获取这两个服务的许可证。 但是，Azure Active Directory Premium 订阅包含一个 PingAccess 基本许可证，可涵盖多达 20 个应用程序。 如果需要发布 20 个以上基于标头的应用程序，可以再从 PingAccess 购买一个许可证。 

有关详细信息，请参阅 [Azure Active Directory 版本](../active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中发布应用程序

本文面向首次使用此方案发布应用的人员。 其中逐步讲解了应用程序和 PingAccess 的入门方法以及发布步骤。 如果已配置这两个服务，但只是想要重温发布步骤，则可以跳过连接器安装，转到[使用应用程序代理将应用添加到 Azure AD](#add-your-app-to-Azure-AD-with-Application-Proxy)。

>[!NOTE]
>由于本方案是 Azure AD 与 PingAccess 之间的合作成果，某些说明已在 Ping Identity 站点上提供。

### <a name="install-an-application-proxy-connector"></a>安装应用程序代理连接器

如果已经启用应用程序代理，并且安装了连接器，则可以跳过此部分，转到[使用应用程序代理将应用添加到 Azure AD](#add-your-app-to-azure-ad-with-application-proxy)。

应用程序代理连接器是一个 Windows Server 服务，可将远程员工的流量定向到已发布的应用。 有关详细的安装说明，请参阅[在 Azure 门户中启用应用程序代理](application-proxy-enable.md)。

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “应用程序代理”。
3. 选择“下载连接器”，开始下载应用程序代理连接器。 遵照安装说明操作。

   ![启用应用程序代理并下载连接器](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. 下载连接器时应会自动为目录启用应用程序代理，但如果没有，选择“启用应用程序代理”。


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>使用应用程序代理将应用添加到 Azure AD

需要在 Azure 门户中执行两项操作。 首先，需要使用应用程序代理发布应用程序。 然后，需要收集一些有关应用的信息，以便在执行 PingAccess 步骤期间使用。

按以下步骤发布应用。 有关步骤 1-8 更详细的演练，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)。

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)（如果在上一部分中未登录）。
2. 选择“Azure Active Directory” > “企业应用程序”。
3. 单击边栏选项卡顶部的“添加”。
4. 选择“本地应用程序”。
5. 在必填的字段中填写有关新应用的信息。 参考以下指导完成设置：
   - **内部 URL**：通常提供在企业网络中时可你将转到应用登录页的 URL。 对于此方案，连接器需要将 PingAccess 代理视为应用的首页。 使用此格式：`https://<host name of your PA server>:<port>`。 默认情况下端口为 3000，但可以在 PingAccess 中对其进行配置。

    > [!WARNING]
    > 对于此类型的 SSO，内部 URL 必须使用 https，不能使用 http。

   - **预身份验证方法**：Azure Active Directory
   - **转换标头中的 URL**：否

   >[!NOTE]
   >如果这是第一个应用程序，则从使用端口 3000 开始，如果更改了 PingAccess 配置，则返回以更新此设置。 如果这是你的第二个或后续应用，需要将其与在 PingAccess 中配置的侦听器相匹配。 详细了解 [PingAccess 中的侦听器](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)。

6. 选择边栏选项卡底部的“添加”。 添加应用程序后，将打开快速启动菜单。
7. 在快速启动菜单中选择“分配用于测试的用户”，并将至少一个用户添加到应用程序。 确保此测试帐户有权访问本地应用程序。
8. 选择“分配”，保存测试用户分配。
9. 在应用管理边栏选项卡中选择“单一登录”。
10. 从下拉菜单中选择“基于标头的登录”。 选择“保存”。

   >[!TIP]
   >如果这是第一次使用基于标头的单一登录，则需安装 PingAccess。 为了确保 Azure 订阅与 PingAccess 安装自动关联，请使用此单一登录页上的链接下载 PingAccess。 可以现在就打开下载站点，也可以稍后返回到此页面。 

   ![选择基于标头的登录](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. 关闭“企业应用程序”边栏选项卡或一直向左滚动，返回“Azure Active Directory”菜单。
12. 选择“应用注册”。

   ![选择“应用注册”](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. 选择刚添加的应用，并选择“回复 URL”。

   ![选择“回复 URL”](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. 检查在步骤 5 中分配到应用的外部 URL 是否出现在“回复 URL”列表中。 如果未出现，现在请添加。
15. 在应用设置边栏选项卡中，选择“所需的权限”。

  ![选择“所需的权限”](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. 选择 **添加** 。 对于 API，请选择“Windows Azure Active Directory”，并单击“选择”。 对于权限，请选择“读取和写入所有应用程序”和“登录并读取用户配置文件”，并依次单击“选择”和“完成”。  

  ![选择权限](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. 关闭权限屏幕之前，请授予权限。 
![授予权限](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步骤的信息

1. 在应用设置边栏选项卡中，选择“属性”。 

  ![选择“属性”](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. 保存“应用程序 ID”值。 配置 PingAccess 时，此值将用作客户端 ID。
3. 在应用设置边栏选项卡中，选择“密钥”。

  ![选择“密钥”](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. 创建密钥：输入密钥的说明，并从下拉菜单中选择过期日期。
5. 选择“保存”。 一个 GUID 随即出现在“值”字段中。

  现在请保存此值，因为关闭此窗口后再也无法看到它。

  ![新建密钥](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. 关闭“应用注册”边栏选项卡或一直向左滚动，返回“Azure Active Directory”菜单。
7. 选择“属性”。
8. 保存“目录 ID”GUID。

### <a name="optional---update-graphapi-to-send-custom-fields"></a>可选 - 更新 GraphAPI 以发送自定义字段

有关 Azure AD 用于身份验证发送的安全令牌列表，请参阅 [Azure AD 令牌引用](./../develop/active-directory-token-and-claims.md)。 如果需要用于发送其他令牌的自定义声明，请使用 Graph 浏览器或 Azure 门户中的应用程序清单，将应用字段“acceptMappedClaims”设置为“True”。    

本示例中使用的是 Graph 浏览器：

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
本示例使用 [Azure 门户](https://portal.azure.com)来更新“acceptedMappedClaims”字段：
1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “应用注册”。
3. 选择应用程序 >“清单”。
4. 选择“编辑”，搜索“acceptedMappedClaims”字段，将其值更改为“true”。
![应用清单](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. 选择“保存”。

>[!NOTE]
>若要使用自定义声明，还必须定义自定义策略并将其分配给应用程序。  此策略应包括所有必需的自定义属性。
>
>可以通过 PowerShell、Azure AD Graph Explorer 或 MS Graph 来完成策略定义和分配。  如果在 PowerShell 中执行此操作，可能需要先使用 `New-AzureADPolicy ` 新建策略，然后使用 `Set-AzureADServicePrincipalPolicy` 将其分配给应用程序。  有关详细信息，请参阅 [Azure AD 策略文档](../active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

### <a name="optional---use-a-custom-claim"></a>可选 - 使用自定义声明
若要生成应用程序，请使用自定义声明并包括其他字段，请确保还[创建了自定义声明映射策略并将其分配给应用程序](../active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

## <a name="download-pingaccess-and-configure-your-app"></a>下载 PingAccess 并配置应用

完成 Azure Active Directory 的所有设置步骤后，可以开始配置 PingAccess。 

Ping Identity 文档 [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)（配置 PingAccess for Azure AD）中为本方案的详细 PingAccess 步骤提供了后续内容。

这些步骤将引导完成获取 PingAccess 帐户（如果尚未获取）、安装 PingAccess 服务器并使用从 Azure 门户中复制的目录 ID 创建 Azure AD OIDC 提供程序连接的整个过程。 然后，在 PingAccess 中使用应用程序 ID 和密钥值创建 Web 会话。 接下来，可以设置标识映射并创建虚拟主机、站点和应用程序。

### <a name="test-your-app"></a>测试应用

完成所有这些步骤后，应用应会启动并运行。 若要测试应用，请打开浏览器并导航到在 Azure 中发布应用该时创建的外部 URL。 使用分配给应用的测试帐户登录。

## <a name="next-steps"></a>后续步骤

- [配置 PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure AD 应用程序代理如何提供单一登录？](application-proxy-single-sign-on.md)
- [排查应用程序代理问题](application-proxy-troubleshoot.md)
