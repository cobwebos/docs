---
title: 使用租户限制访问管理对 SaaS 云应用程序-Azure |Microsoft Docs
description: 如何使用租户限制来管理哪些用户可以访问基于其 Azure AD 租户的应用。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824386"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租户限制来管理对 SaaS 云应用程序的访问

重视安全的大型组织都希望迁移到 Office 365 等云服务中，但需要知道，其用户只能访问已批准的资源。 在传统上，公司在想要管理访问权限时，会限制域名或 IP 地址。 此方法失败在公有云中，共享的域名，如运行托管软件即服务 （或 SaaS） 应用的世界[outlook.office.com](https://outlook.office.com/)并[login.microsoftonline.com](https://login.microsoftonline.com/)。 阻止这些地址会导致用户完全无法访问 Web 上的 Outlook，而不只是将他们能够访问的内容局限于批准的标识和资源。

Azure Active Directory (Azure AD) 解决这个难题是名为租户限制的功能。 使用租户限制，组织可以控制对 SaaS 云应用程序，根据应用程序使用的单一登录的 Azure AD 租户的访问。 例如，你可能希望允许用户访问你所在组织的 Office 365 应用程序，同时阻止他们访问其他组织中这些应用程序的实例。  

使用租户限制，组织可以指定其用户有权访问的租户的列表。 然后，Azure AD 只会授予对这些允许的租户的访问权限。

本文重点介绍适用于 Office 365 租户限制，但此功能应适用于任何与 Azure AD 进行单一登录使用新式身份验证协议的 SaaS 云应用。 如果 SaaS 应用使用的 Azure AD 租户不同于 Office 365 使用的租户，请确保允许全部所需的租户。 有关 SaaS 云应用的详细信息，请参阅 [Active Directory 市场](https://azure.microsoft.com/marketplace/active-directory/)。

## <a name="how-it-works"></a>工作原理

总体解决方案由以下组件构成：

1. **Azure AD**：如果`Restrict-Access-To-Tenants: <permitted tenant list>`针对允许的租户中已存在，Azure AD 只能颁发安全令牌。

2. **在本地代理服务器基础结构**:此基础结构是支持安全套接字层 (SSL) 检查的代理设备。 必须配置要插入标头允许的租户的列表包含流量发往 Azure AD 的代理。

3. **客户端软件**:若要支持租户限制，客户端软件必须请求令牌直接从 Azure AD，使代理基础结构可以截获流量。 基于浏览器的 Office 365 应用程序目前支持租户限制，使用 OAuth 2.0 等新式身份验证的 Office 客户端。

4. **新式身份验证**:云服务必须使用新式身份验证来使用租户限制，阻止对所有不允许的租户的访问。 必须配置 Office 365 云服务，默认情况下使用新式身份验证协议。 有关 Office 365 对新式身份验证的支持的最新信息，请阅读 [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)（更新的 Office 365 新式身份验证）。

下图演示了大致的流量流。 租户限制需要仅在流量到 Azure AD 上，而不是 Office 365 云服务上的 SSL 检查。 这一区别很重要，因为 Azure AD 身份验证的流量卷是通常比 SaaS 应用程序，如 Exchange Online 和 SharePoint Online 的通信流量低得多。

![租户限制流量流-关系图](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>设置租户限制

有两个步骤，若要开始使用租户限制。 首先，请确保你的客户端可以连接到正确的地址。 其次，配置代理基础结构。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 地址

若要使用租户限制，客户端必须能够连接到以下 Azure AD Url 进行身份验证： [login.microsoftonline.com](https://login.microsoftonline.com/)， [login.microsoft.com](https://login.microsoft.com/)，和[login.windows.net](https://login.windows.net/)。 此外，访问 Office 365，你的客户端还必须能够连接到的完全限定的域名 (Fqdn) Url，并在定义的 IP 地址[Office 365 Url 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。 

### <a name="proxy-configuration-and-requirements"></a>代理配置和要求

下面的配置需要启用租户限制通过代理基础结构。 本指南的内容是概括性的，有关具体的实施步骤，请参阅代理供应商的文档。

#### <a name="prerequisites"></a>必备组件

- 代理必须能够使用 FQDN/URL 执行 SSL 截获，HTTP 标头插入和目标筛选。

- 客户端必须信任代理提供的用于 SSL 通信的证书链。 例如，如果从内部证书[公钥基础结构 (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure)是使用，内部颁发根证书颁发机构证书必须受信任。

- 此功能包含在 Office 365 订阅，但如果您想要使用租户限制来控制对其他 SaaS 应用的访问，则需要 Azure AD Premium 1 许可证。

#### <a name="configuration"></a>配置

对于 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的每个传入请求，请插入两个 HTTP 标头：“Restrict-Access-To-Tenants”和“Restrict-Access-Context”。

这些标头应包含以下元素：

- 有关*限制租户访问*，使用值\<允许租户列表\>，这是你想要允许用户访问的租户的逗号分隔列表。 已注册到某个租户的任何域都可用于在此列表中标识该租户。 例如，若要允许访问 Contoso 和 Fabrikam 租户，名称/值对如下所示： `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- 有关*限制访问权限上下文*，使用单个目录 ID 的值，则声明哪个租户设置租户限制。 例如，要将 Contoso 声明为设置租户限制策略的租户，名称/值对如下所示： `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 您可以发现在你的 directory ID [Azure Active Directory 门户](https://aad.portal.azure.com/)。 以管理员身份登录，选择“Azure Active Directory”，再选择“属性”即可。

若要防止用户将他们自己的 HTTP 标头插入与未批准的租户，代理需要替换*限制租户访问*标头，如果已传入的请求中存在。

必须强制客户端针对发往 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的所有请求使用代理。 例如，如果使用 PAC 文件来指示客户端使用代理服务器，最终用户应当能够编辑或禁用这些 PAC 文件。

## <a name="the-user-experience"></a>用户体验

本部分介绍最终用户和管理员的体验。

### <a name="end-user-experience"></a>最终用户体验

例如，某个用户身处 Contoso 网络，但该用户正在尝试访问 Fabrikam 的某个共享 SaaS 应用程序的实例（如 Outlook Online）。 如果 Fabrikam 是 Contoso 实例不允许租户，用户将看到访问拒绝消息，指出你正在尝试访问属于组织的 IT 部门未批准的资源。

### <a name="admin-experience"></a>管理员体验

虽然企业代理基础结构上进行配置的租户限制，管理员可以直接访问 Azure 门户中的租户限制报告。 若要查看的报表：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 **Azure Active Directory 管理中心**仪表板将出现。

2. 在左窗格中选择“Azure Active Directory”。 Azure Active Directory 概述页会显示。

3. 在中**其他功能**标题下方，选择**租户限制**。

指定为受限制访问权限上下文租户可以使用此报表以查看由于租户限制策略，包括使用的标识已阻止登录的租户的管理员和目标目录 id。 如果设置限制的租户是登录的用户租户或资源租户，则会包含登录。

与使用 Azure 门户中的其他报告时一样，可以使用筛选器来指定报告的范围。 您可以筛选特定的时间间隔、 用户、 应用程序、 客户端或状态。 如果选择**列**按钮，您可以选择显示以下字段的任意组合的数据：

- **User**
- **应用程序**
- **Status**
- **日期**
- **日期 (UTC)** （其中 UTC 是协调世界时）
- **MFA 身份验证方法**（多重身份验证方法）
- **MFA 身份验证详细信息**（多重身份验证详细信息）
- **MFA 结果**
- **IP 地址**
- **客户端**
- **用户名**
- **位置**
- **目标租户 ID**

## <a name="office-365-support"></a>Office 365 支持

Office 365 应用程序必须满足两个条件才能完全支持租户限制：

1. 使用的客户端支持新式身份验证。
2. 已启用新式身份验证作为云服务的默认身份验证协议。

有关哪些 Office 客户端目前支持新式身份验证的最新信息，请参阅 [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)（更新的 Office 365 新式身份验证）。 该页面还包含有关如何在特定的 Exchange Online 和 Skype for Business Online 租户中启用新式身份验证的说明文档链接。 SharePoint Online 已启用新式身份验证默认情况下。

Office 365 基于浏览器的应用程序 （Office 门户、 Yammer、 SharePoint 网站、 Outlook Web 应用程序和的详细信息） 目前支持租户限制。 仅当使用新式身份验证时，胖客户端 （Outlook、 Skype 的业务、 Word、 Excel、 PowerPoint 和的详细信息） 可以强制实施租户限制。  

Outlook 和 Skype for Business 客户端支持新式身份验证，可能仍可以使用传统协议对租户启用新式身份验证不是，有效地绕过租户限制。 租户限制可能会阻止使用传统协议，如果在身份验证期间联系 login.microsoftonline.com、 login.microsoft.com 或 login.windows.net 的应用程序。

对于 Windows 上的 Outlook，客户可能选择实施限制来阻止最终用户将未经批准的邮件帐户添加到其配置文件。 有关示例，请参阅[阻止添加非默认 Exchange 帐户](https://gpsearch.azurewebsites.net/default.aspx?ref=1)组策略设置。

## <a name="testing"></a>正在测试

如果你想要在整个组织实施之前试用租户限制，有两个选项： 使用 Fiddler 或分阶段的部署代理设置等工具基于主机的方法。

### <a name="fiddler-for-a-host-based-approach"></a>基于主机的实施方法所用的 Fiddler

Fiddler 是一个免费 Web 调试代理，可用于捕获和修改 HTTP/HTTPS 流量，包括插入 HTTP 标头。 若要配置 Fiddler 来测试租户限制，请执行以下步骤：

1. [下载并安装 Fiddler](https://www.telerik.com/fiddler)。

2. 根据 [Fiddler 的帮助文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)，配置 Fiddler 来解密 HTTPS 流量。

3. 配置 Fiddler，使用自定义规则插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 标头：

   1. 在 Fiddler Web 调试器工具中选择“规则”菜单，并选择“自定义规则...” 打开 CustomRules 文件。

   2. 在开头添加以下行`OnBeforeRequest`函数。 替换\<租户域\>与你的租户注册的域 (例如， `contoso.onmicrosoft.com`)。 将 \<directory ID\> 替换为租户的 Azure AD GUID 标识符。

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      如果需要允许多个租户，请使用逗号分隔租户名称。 例如:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. 保存并关闭 CustomRules 文件。

配置 Fiddler 后，可转到“文件”菜单并选择“捕获流量”来捕获流量。

### <a name="staged-rollout-of-proxy-settings"></a>分阶段部署代理设置

根据代理基础结构的功能，有时可以分阶段向用户部署设置。 请考虑下面几个高级选项：

1. 使用 PAC 文件将测试用户指向测试代理基础结构，普通用户可继续使用生产代理基础结构。
2. 某些代理服务器可以使用组来支持不同的配置。

有关特定详细信息，请参阅代理服务器文档。

## <a name="next-steps"></a>后续步骤

- 阅读 [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)（更新的 Office 365 新式身份验证）
- 查看 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
