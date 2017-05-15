---
title: "使用 PingAccess for Azure AD 应用程序代理进行基于标头的身份验证 | Microsoft Docs"
description: "使用 PingAccess 和应用代理发布应用程序，以支持基于标头的身份验证。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 8db76d1f83cdf1cf53ddd1e9c69c56400d04af2d
ms.contentlocale: zh-cn
ms.lasthandoff: 04/29/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-azure-ad-application-proxy-and-pingaccess"></a>使用 Azure AD 应用程序代理和 PingAccess 发布支持基于标头的身份验证的应用程序

将 Azure Active Directory 应用程序代理与 PingAccess 配合使用可让 Azure Active Directory 客户访问更多的应用程序。 PingAccess 扩展了[现有的应用程序代理产品](active-directory-application-proxy-get-started.md)，包含对使用标头进行身份验证的应用程序的远程访问功能。 

## <a name="what-is-pingaccess-for-azure-ad"></a>什么是 PingAccess for Azure AD？

若要让用户访问使用标头进行身份验证的应用，可在应用程序代理和 PingAccess 中发布用于远程访问的应用。 应用程序代理将这些应用视为与其他任何应用一样，它使用 Azure AD 对访问进行身份验证，然后通过连接器服务传递流量。 PingAccess 驻留在应用的前面，可将 Azure AD 提供的访问令牌转换为标头，使应用程序能够接收采用可读格式的身份验证令牌。 

用户登录后使用你的企业应用时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。 当用户在办公室时，应用程序代理和 PingAccess 都不会截获流量，因此用户自始至终都会获得相同的体验。

由于应用程序代理连接器会将远程流量定向到所有应用（不管其身份验证类型为何），因此也会继续自动进行负载均衡。 

## <a name="how-do-i-get-access"></a>如何获取访问权限？

由于本方案是通过 Azure Active Directory 与 PingAccess 之间的合作提供的，因此需要获取这两个服务的许可证。 Azure Active Directory Premium 订阅随附 PingAccess 初学者许可证，让你在此流程中最多配置 20 个应用程序。 

有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

## <a name="publish-your-first-application"></a>发布第一个应用程序

本文面向首次使用此方案发布应用的人员。 其中逐步讲解了应用程序和 PingAccess 的入门方法以及发布步骤。 如果你已配置这两个服务但只是想要重温发布步骤，可以跳过有关注册的两个部分。

>[!NOTE]
>由于本方案是 Azure AD 与 PingAccess 之间的合作成果，某些说明已在 Ping Identity 站点上提供。 

### <a name="install-an-application-proxy-connector"></a>安装应用程序代理连接器

如果已启用应用程序代理并已安装连接器，可以跳过这些步骤。

应用程序代理连接器是一个 Windows Server 服务，可将远程员工的流量定向到已发布的应用。 有关详细的安装说明，请参阅[在 Azure 门户中启用应用程序代理](active-directory-application-proxy-enable.md)。

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。 
2. 选择“Azure Active Directory” > “应用程序代理”。
3. 选择“下载连接器”，开始下载应用程序代理连接器。 遵照安装说明操作。 
4. 下载连接器时应会自动为目录启用应用程序代理，但如果没有，选择“启用应用程序代理”。 

![启用应用程序代理并下载连接器](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>使用应用程序代理将应用添加到 Azure AD

此过程由两个部分组成。 首先，需要将应用发布到 Azure AD。 然后，需要收集一些有关应用的信息，以便在执行 PingAccess 步骤期间使用。 

#### <a name="publish-the-app"></a>发布应用

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)（如果在上一部分中未登录）。 
2. 选择“Azure Active Directory” > “企业应用程序”。 
3. 单击边栏选项卡顶部的“添加”。 
4. 选择“本地应用程序”。
5. 在必填的字段中填写有关新应用的信息。 参考以下指导完成设置：
  - **内部 URL**：通常提供在企业网络中时可将你转到应用登录页的 URL。 对于此合作关系，连接器需要将 PingAccess 代理视为应用的首页。 使用此格式：`https://<host name of your PA server>:<port>/<App path name>`。 默认情况下端口为 3000，但可以在 PingAccess 中对其进行配置。
  - **预身份验证方法**：Azure Active Directory
  - **转换标头中的 URL**：否
6. 选择边栏选项卡底部的“添加”。 添加应用程序后，将打开快速启动菜单。 
7. 在快速启动菜单中选择“分配用于测试的用户”，并将至少一个用户添加到应用程序。 确保此测试帐户有权访问本地应用程序。 
8. 选择“分配”，保存测试用户分配。 
9. 在应用管理边栏选项卡中选择“单一登录”。 
10. 从下拉菜单中选择“基于标头的登录”。 选择“保存”。

  ![选择基于标头的登录](./media/application-proxy-ping-access/sso-header.PNG)

11. 关闭“企业应用程序”边栏选项卡或一直向左滚动，返回“Azure Active Directory”菜单。 
12. 选择“应用注册”。
13. 选择刚添加的应用，然后选择“回复 URL”。 
14. 检查在步骤 5 中分配到应用的外部 URL 是否出现在“回复 URL”列表中。 如果未出现，现在请添加。 
15. 在应用设置边栏选项卡中，选择“所需的权限”。 
16. 选择“添加”。 对于 API，请选择“Windows Azure Active Directory”，然后单击“选择”。 对于权限，请选择“读取和写入所有应用程序”和“登录并读取用户配置文件”，然后依次单击“选择”和“完成”。  

  ![选择权限](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步骤的信息

1. 在应用设置边栏选项卡中，选择“属性”。 保存“应用程序 ID”值。 配置 PingAccess 时，此值将用作客户端 ID。
2. 在应用设置边栏选项卡中，选择“密钥”。 
3. 创建密钥：输入密钥的说明，并从下拉菜单中选择过期日期。 
4. 选择“保存”。 一个 GUID 随即出现在“值”字段中。 

  现在请保存此值，因为关闭此窗口后再也无法看到它。 

5. 关闭“应用注册”边栏选项卡或一直向左滚动，返回“Azure Active Directory”菜单。
6. 选择“属性”。
7. 保存“目录 ID”GUID。 

### <a name="download-pingaccess-and-configure-your-app"></a>下载 PingAccess 并配置应用

Ping Identity 文档 [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)（配置 PingAccess for Azure AD）中为本方案的详细 PingAccess 步骤提供了后续内容。

这些步骤将引导你完成获取 PingAccess 帐户（如果尚未获取）、安装 PingAccess 服务器并使用从 Azure 门户中复制的目录 ID 创建 Azure AD OIDC 提供程序连接的整个过程。 然后，可以在 PingAccess 中使用应用程序 ID 和密钥值创建 Web 会话。 接下来，可以设置标识映射并创建虚拟主机、站点和应用程序。

### <a name="test-your-app"></a>测试应用程序

完成所有这些步骤后，你的应用应会启动并运行。 若要测试应用，请打开浏览器并导航到在 Azure 中发布应用该时创建的外部 URL。 使用分配给应用的测试帐户登录。 

## <a name="next-steps"></a>后续步骤

- [排查应用程序代理问题](active-directory-application-proxy-troubleshoot.md)

