---
title: 通过限制租户来管理对云应用的访问 - Azure | Microsoft 文档
description: 如何使用租户限制来根据用户的 Azure AD 租户管理可访问应用的用户。
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: kgremban
ms.openlocfilehash: dae4599db5127ac8fd266d5e0f299e1284fc9b9c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租户限制管理对 SaaS 云应用程序的访问

重视安全的大型组织都希望迁移到 Office 365 等云服务中，但需要知道，其用户只能访问已批准的资源。 在传统上，公司在想要管理访问权限时，会限制域名或 IP 地址。 如果 SaaS 应用在公有云中托管并在类似于 outlook.office.com 和 login.microsoftonline.com 的共享域名中运行，则这种做法会失败。阻止这些地址会导致用户完全无法访问 Web 上的 Outlook，而不只是将他们能够访问的内容局限于批准的标识和资源。

为了解决这个难题，Azure Active Directory 推出了一项称作“租户限制”的功能。 租户限制可让组织根据应用程序用于单一登录的 Azure AD 租户来控制对 SaaS 云应用程序的访问。 例如，你可能希望允许用户访问你所在组织的 Office 365 应用程序，同时阻止他们访问其他组织中这些应用程序的实例。  

借助租户限制，组织可以指定其用户有权访问的租户的列表。 然后，Azure AD 只会授予对这些允许的租户的访问权限。

本文重点介绍 Office 365 的租户限制，但该功能应该也适用于结合 Azure AD 使用新式身份验证协议进行单一登录的任何 SaaS 云应用。 如果 SaaS 应用使用的 Azure AD 租户不同于 Office 365 使用的租户，请确保允许全部所需的租户。 有关 SaaS 云应用的详细信息，请参阅 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)（Active Directory 应用商店）。

## <a name="how-it-works"></a>工作原理

总体解决方案由以下组件构成： 

1. **Azure AD** – 如果指定了 `Restrict-Access-To-Tenants: <permitted tenant list>`，Azure AD 只会针对允许的租户颁发安全令牌。 

2. **本地代理服务器基础结构** – 支持 SSL 检查的代理设备，配置为在发往 Azure AD 租户的流量中插入包含受允许租户列表的标头。 

3. **客户端软件** – 为了支持租户限制，客户端软件必须直接从 Azure AD 请求令牌，使代理基础结构能够截获流量。 目前，基于浏览器的 Office 365 应用程序以及使用 OAuth 2.0 等新式身份验证的 Office 客户端支持租户限制。 

4. **新式身份验证** – 云服务必须使用新式身份验证来使用租户限制，阻止对所有不允许的租户的访问。 必须将 Office 365 云服务配置为默认使用新式身份验证协议。 有关 Office 365 对新式身份验证的支持的最新信息，请阅读 [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)（更新的 Office 365 新式身份验证）。

下图演示了大致的流量流。 只需针对发往 Azure AD 的流量执行 SSL 检查，对于发往 Office 365 云服务的流量，不需要执行此检查。 这种区别很重要，因为 Azure AD 的身份验证流量通常比 SaaS 应用程序（如 Exchange Online 和 SharePoint Online）的流量要小得多。

![租户限制流量流 - 示意图](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>设置租户限制

需要完成两个步骤才能开始使用租户限制。 第一个步骤是确保客户端能够连接到正确的地址。 第二个步骤是配置代理基础结构。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 地址

若要使用租户限制，客户端必须能够连接到以下 Azure AD URL 进行身份验证：login.microsoftonline.com、login.microsoft.com 和 login.windows.net。 此外，若要访问 Office 365，客户端还必须能够连接到 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中定义的 FQDN/URL 和 IP 地址。 

### <a name="proxy-configuration-and-requirements"></a>代理配置和要求

需要完成以下配置，以便通过代理基础结构启用租户限制。 本指南的内容是概括性的，有关具体的实施步骤，请参阅代理供应商的文档。

#### <a name="prerequisites"></a>先决条件

- 代理必须能够使用 FQDN/URL 执行 SSL 截获，HTTP 标头插入和目标筛选。 

- 客户端必须信任代理提供的用于 SSL 通信的证书链。 例如，如果使用了来自内部 PKI 的证书，则必须受信任内部颁发根证书颁发机构证书。

- Office 365 订阅中包含此功能，但如果想要使用租户限制来控制对其他 SaaS 应用的访问，则需要提供 Azure AD Premium 1 许可证。

#### <a name="configuration"></a>配置

对于 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的每个传入请求，请插入两个 HTTP 标头：*Restrict-Access-To-Tenants* 和 *Restrict-Access-Context*。

这些标头应包含以下元素： 
- 对于 *Restrict-Access-To-Tenants*，应包含\<允许的租户列表\>值，这是希望允许用户访问的租户的逗号分隔列表。 已注册到某个租户的任何域都可用于在此列表中标识该租户。 例如，若要允许访问 Contoso 和 Fabrikam 租户，名称/值对如下所示：`Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- 对于 *Restrict-Access-Context*，应包含单个目录 ID 的值，用于声明哪个租户将要设置租户限制。 例如，要将 Contoso 声明为要设置租户限制策略的租户，名称/值对如下所示：`Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 可在 [Azure 门户](https://portal.azure.com)中找到该目录 ID。 以管理员身份登录，选择“Azure Active Directory”，再选择“属性”即可。

为了防止用户插入其自己的、包含未批准的租户的 HTTP 标头，代理需要替换 Restrict-Access-To-Tenants 标头（如果传入的请求中已提供此标头）。 

必须强制客户端针对发往 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的所有请求使用代理。 例如，如果使用 PAC 文件来指示客户端使用代理，则不应该允许最终用户编辑或禁用这些 PAC 文件。

## <a name="the-user-experience"></a>用户体验

本部分介绍最终用户和管理员的体验。

### <a name="end-user-experience"></a>最终用户体验

例如，某个用户身处 Contoso 网络，但该用户正在尝试访问 Fabrikam 的某个共享 SaaS 应用程序的实例（如 Outlook Online）。 如果 Contoso 不是该实例的受允许租户，该用户将看到以下页面：

![用户在不允许的租户中看到的拒绝访问页面](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>管理员体验

尽管租户限制的配置是在企业代理基础结构中完成的，但管理员也可以直接在 Azure 门户中访问租户限制报告。 要查看报告，请转到 Azure Active Directory 的“概述”页，并查看“其他功能”。

Restricted-Access-Context 租户的管理员可以使用此报告来查看由于租户限制策略的原因而被阻止的所有登录，包括使用的标识和目标目录 ID。

![使用 Azure 门户查看受限制的登录尝试](./media/active-directory-tenant-restrictions/portal-report.png)

与使用 Azure 门户中的其他报告时一样，可以使用筛选器来指定报告的范围。 可以根据特定的用户、应用程序、客户端或时间间隔进行筛选。

## <a name="office-365-support"></a>Office 365 支持

Office 365 应用程序必须满足两个条件才能完全支持租户限制：

1. 使用的客户端支持新式身份验证
2. 已启用新式身份验证作为云服务的默认身份验证协议。

有关哪些 Office 客户端目前支持新式身份验证的最新信息，请参阅 [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)（更新的 Office 365 新式身份验证）。 该页面还包含有关如何在特定的 Exchange Online 和 Skype for Business Online 租户中启用新式身份验证的说明文档链接。 在 SharePoint Online 中，默认已启用新式身份验证。

Office 365 基于浏览器的应用程序（Office 门户、Yammer、SharePoint 网站、Outlook 网页版等）目前支持租户限制。 胖客户端（Outlook、Skype for Business、Word、Excel、 PowerPoint 等）仅当使用新式身份验证时，才能实施租户限制。  

支持新式身份验证的 Outlook 和 Skype for Business 客户端仍可针对未启用新式身份验证的租户使用传统协议，从而有效绕过租户限制。 如果在身份验证期间访问 login.microsoftonline.com、login.microsoft.com 或 login.windows.net，使用传统协议的应用程序可能会被租户限制阻止。

对于 Windows 上的 Outlook，客户可能选择实施限制来阻止最终用户将未经批准的邮件帐户添加到其配置文件。 有关示例，请参阅[阻止添加非默认 Exchange 帐户](http://gpsearch.azurewebsites.net/default.aspx?ref=1)组策略设置。 对于非 Windows 平台上的 Outlook 和所有平台上的 Skype for Business，对租户限制的完全支持当前不可用。

## <a name="testing"></a>测试

在整个组织中实施租户限制之前如果想要试用，可采取两种做法：使用 Fiddler 等工具基于主机实施租户限制，或者分阶段部署代理设置。

### <a name="fiddler-for-a-host-based-approach"></a>基于主机的实施方法所用的 Fiddler

Fiddler 是一个免费 Web 调试代理，可用于捕获和修改 HTTP/HTTPS 流量，包括插入 HTTP 标头。 若要配置 Fiddler 来测试租户限制，请执行以下步骤：

1.  [下载并安装 Fiddler](http://www.telerik.com/fiddler)。
2.  根据 [Fiddler 的帮助文档](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)，配置 Fiddler 来解密 HTTPS 流量。
3.  配置 Fiddler，使用自定义规则插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 标头：
  1. 在 Fiddler Web 调试器工具中选择“规则”菜单，并选择“自定义规则...” 打开 CustomRules 文件。
  2. 在 *OnBeforeRequest* 函数的开头添加以下行。 将 \<tenant domain\> 替换为已注册到租户的域，例如 contoso.onmicrosoft.com。将 \<directory ID\> 替换为租户的 Azure AD GUID 标识符。

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  如果需要允许多个租户，请使用逗号分隔租户名称。 例如：

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. 保存并关闭 CustomRules 文件。

配置 Fiddler 后，可转到“文件”菜单并选择“捕获流量”来捕获流量。

### <a name="staged-rollout-of-proxy-settings"></a>分阶段部署代理设置

根据代理基础结构的功能，有时可以分阶段向用户部署设置。 请考虑下面几个高级选项：

1.  使用 PAC 文件将测试用户指向测试代理基础结构，普通用户可继续使用生产代理基础结构。
2.  某些代理服务器可以使用组来支持不同的配置。

有关具体细节，请参阅代理服务器文档。

## <a name="next-steps"></a>后续步骤

- 阅读 [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)（更新的 Office 365 新式身份验证）

- 查看 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
