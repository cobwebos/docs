---
title: 排查 Azure Web 应用中的域和 SSL 证书问题 | Microsoft Docs
description: 排查 Azure Web 应用中的域和 SSL 证书问题
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>排查 Azure Web 应用中的域和 SSL 证书问题

本文列出了为 Azure Web 应用配置域或 SSL 证书时可能遇到的常见问题。 此外，还描述了这些问题的可能原因和解决方法。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)，并单击“**获取支持**”。

## <a name="certificate-problems"></a>证书问题

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>无法将 SSL 绑定证书添加到 Web 应用 

### <a name="symptom"></a>症状

添加 SSL 绑定时出现以下错误消息：

“未能添加 SSL 绑定。无法设置现有 VIP 的证书，因为另一个 VIP 已使用此证书。”

### <a name="cause"></a>原因

如果对跨多个 Web 应用的同一 IP 地址使用多个基于 IP 的 SSL 绑定，则可能会出现此问题。 例如，Web 应用 A 具有采用旧证书的基于 IP 的 SSL。 Web 应用 B 对同一 IP 地址使用采用新证书的基于 IP 的 SSL。 使用新证书更新 Web 应用 SSL 绑定时，此操作将会失败并出现此错误，因为同一个 IP 地址已由另一应用使用。 

### <a name="solution"></a>解决方案 

若要解决此问题，请使用以下方法之一：

- 在 Web 应用中删除使用旧证书的基于 IP 的 SSL 绑定。 
- 新建使用新证书的基于 IP 的 SSL 绑定。

### <a name="unable-to-delete-a-certificate"></a>无法删除证书 

### <a name="symptom"></a>症状

尝试删除证书时出现以下错误消息：

“无法删除证书，因为它当前正用于 SSL 绑定。删除证书之前，必须删除 SSL 绑定。”

### <a name="cause"></a>原因

如果证书已由另一 Web 应用使用，则可能会出现此问题。

### <a name="solution"></a>解决方案

从 Web 应用中删除该证书的 SSL 绑定。 然后尝试删除证书。 如果仍然无法删除证书，请清除 Internet 浏览器缓存，并在新浏览器窗口中重新打开 Azure 门户。 然后尝试删除证书。

### <a name="unable-to-purchase-an-app-service-certificate"></a>无法购买应用服务证书 

### <a name="symptom"></a>症状
无法从 Azure 门户购买[应用服务证书](./web-sites-purchase-ssl-web-site.md)。

### <a name="cause-and-solution"></a>原因和解决方法
此问题可能是由以下任何原因导致的：

- 应用服务计划的层级为“免费”或“共享”。 这些定价层不支持 SSL。 

    **解决方法**：将 Web 应用的应用服务计划升级到“标准”。

- 订阅中没有有效的信用卡。

    **解决方法**：将有效的信用卡添加到订阅。 

- 订阅产品/服务不支持购买“Microsoft 学生”等应用服务证书。  

    **解决方法**：升级订阅。 

- 订阅已达到允许的最大购买限制。

    **解决方法**：对于即用即付和 EA 订阅类型，可购买的应用服务证书限制为 10 个。 对于其他订阅类型，限制为 3 个。 若要提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 应用服务证书标记为欺诈。 收到以下错误消息：“证书已被标记为可能存在欺诈。 请求当前正在审查中。 如果证书未在 24 小时内变为可用...”

    **解决方法**：如果证书标记为欺诈，并且在 24 小时后未得到解决，请遵循以下步骤：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 转到“应用服务证书”，选择该证书。
    3. 选择“证书配置” > “步骤 2: 验证” > “域验证”。 系统会向 Azure 证书提供者发送一份电子邮件通知，让他们解决问题。

## <a name="domain-problems"></a>域问题

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>为错误的域购买了 SSL 证书

### <a name="symptom"></a>症状

为错误的域购买了应用服务证书，并且无法将该证书更新为使用正确的域。

### <a name="solution"></a>解决方案

- 删除该证书，然后购买新证书。
- 如果使用错误域的当前证书处于“已颁发”状态，则该证书也会产生费用。 应用服务证书不可退款，但你可以联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，看看是否还有其他解决办法。 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>应用服务证书已续订，但仍显示旧证书 

### <a name="symptom"></a>症状

应用服务证书已续订，但使用应用服务证书的 Web 应用仍在使用旧证书。 此外，出现需要 HTTPS 协议的警告。

### <a name="cause"></a>原因 
Web 应用服务每隔 8 小时会运行一个后台作业，如果有任何更改发生，则会同步证书资源。 因此，在轮换或更新证书时，应用程序有时仍会检索旧证书，而不是最近更新的证书。 这是因为同步证书资源的作业尚未运行。 
 
### <a name="solution"></a>解决方案

可以强制同步证书：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“应用服务证书”，然后选择该证书。
2. 依次单击“重新生成密钥和同步”、“同步”。此过程需要一段时间才能完成。 
3. 完成同步后，会看到以下通知：“已成功使用最新的证书更新了所有资源”。

### <a name="domain-verification-is-not-working"></a>域验证无法进行 

### <a name="symptom"></a>症状 
应用服务证书要求先经过域验证，然后该证书才可供使用。 单击“验证”时，验证过程失败。

### <a name="solution"></a>解决方案
通过添加 TXT 记录来手动验证域：
 
1.  转到托管域名的域名服务 (DNS) 提供商站点。
2.  添加域的 TXT 记录，并在其中使用 Azure 门户中显示的域令牌值。 

等待几分钟以运行 DNS 传播，然后单击“刷新”按钮触发验证。 

另一种手动验证方法是使用“HTML 网页方法”让证书颁发机构确认为其颁发证书的域的域所有权。

1.  创建名为 {Domain Verification Token}.html 的 HTML 文件。 
2.  此文件的内容应为域验证令牌的值。
3.  将此文件上传到托管域的 Web 服务器的根目录。
4.  单击“刷新”检查证书状态。 验证可能需要几分钟才能完成。

例如，如果为 azure.com 购买了域验证令牌为“1234abcd”的标准证书，则对 http://azure.com/1234abcd.html 发出的 Web 请求将返回 1234abcd。 

> [!IMPORTANT]
> 下达证书订单后，只有 15 天时间用于完成域验证操作。 15 天过后，证书将被证书颁发机构拒绝，但该证书不会产生费用。 在此情况下，请删除该证书并重试。
>
> 

### <a name="unable-to-purchase-a-domain"></a>无法购买某个域

### <a name="symptom"></a>症状
无法在 Azure 门户中从 Web 应用或应用服务域购买域。

### <a name="cause-and-solution"></a>原因和解决方法

此问题是由以下原因之一导致的：

- Azure 订阅中没有信用卡，或信用卡无效。

    **解决方法**：将有效的信用卡添加到订阅（如果订阅中没有信用卡）。

- 如果你不是订阅所有者，可能无权购买域。

    **解决方法**：[将“所有者”角色添加](../billing/billing-add-change-azure-subscription-administrator.md)到自己的帐户，或联系订阅管理员获取购买域的权限。
- 已达到订阅中可购买域数的限制。 当前限制为 20 个。

    **解决方法**：若要提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- Azure 订阅类型不支持购买应用服务域。

    **解决方法**：将 Azure 订阅升级到其他订阅类型，例如即用即付订阅。

### <a name="unable-to-add-a-hostname-to-web-app"></a>无法将主机名添加到 Web 应用 

### <a name="symptom"></a>症状

在添加主机名的过程中无法验证域。

### <a name="cause"></a>原因 

此问题是由以下原因之一导致的：

- 无权添加主机名。

    **解决方法**：咨询订阅管理员，确保有权添加主机名。
- 无法验证域所有权。

    **解决方法**：验证是否已正确配置 CNAME 或 A 记录。 若要将自定义域映射到 Web 应用，请创建 CNAME 或 A 记录。 若要使用根域，必须使用 A 和 TXT 记录：

    |记录类型|主机|指向|
    |------|------|-----|
    |A|@|Web 应用的 IP 地址|
    |TXT|@|<应用名称>.azurewebsites.net|
    |CNAME|www|<应用名称>.azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>无法解析 DNS

### <a name="symptom"></a>症状

收到“找不到 DNS 记录”错误消息。

### <a name="cause"></a>原因
此问题是由以下原因之一导致的：

- 生存期 (TTL) 未过。 检查域的 DNS 配置以确定 TTL 值，然后等到期限已过。
- DNS 配置不正确。

### <a name="solution"></a>解决方案
- 等待 48 小时，让此问题自行解决。
- 如果可以在 DNS 配置中更改 TTL 设置，请将值更改为 5 分钟，然后看看是否能解决问题。
- 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 验证域是否指向 Web 应用 IP 地址。 如果不是，请将 A 记录配置为 Web 应用的正确 IP 地址。

### <a name="restore-a-deleted-domain"></a>还原已删除的域 

### <a name="symptom"></a>症状
域不再显示在 Azure 门户中。

### <a name="cause"></a>原因 
订阅所有者可能意外删除了该域。

### <a name="solution"></a>解决方案
如果域的删除时间不超过七天，则尚未对该域启动删除过程。 在这种情况下，可以在 Azure 门户中的同一个订阅下购买同一个域（请务必在搜索框中键入确切的域名）。 此域不会重复产生费用。 如果该域的删除时间超过七天，请求助 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来还原该域。

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>自定义域返回 404 或站点不可访问错误 

### <a name="symptom"></a>症状

使用自定义域名浏览到站点时，收到以下错误消息：

“错误 404 - 找不到 Web 应用”。


### <a name="cause-and-solution"></a>原因和解决方法

**原因 1** 

配置的自定义域缺少 CNAME 或 A 记录。 

**原因 1 的解决方法**

- 如果添加了 A 记录，请确保同时添加 TXT 记录。 有关详细信息，请参阅 [Create-the-a-record](./app-service-web-tutorial-custom-domain.md#create-the-a-record)。
- 如果不需要对 Web 应用使用根域，我们建议使用 CNAME 记录，而不要使用 A 记录。
- 不要对同一个域中同时使用 CNAME 和 A 记录。 这可能会导致冲突，并阻止域解析。 

**原因 2** 

Internet 浏览器可能仍在缓存域的旧 IP 地址。 

**原因 2 的解决方法**

清除浏览器缓存。 对于 Windows 设备，可以运行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 验证域是否指向 Web 应用 IP 地址。 

### <a name="unable-to-add-subdomain"></a>无法添加子域 

### <a name="symptom"></a>症状

无法将新主机名添加到 Web 应用以分配子域。

### <a name="solution"></a>解决方案

- 咨询订阅管理员，确保有权将主机名添加到 Web 应用。
- 如果需要更多子域，我们建议将域托管服务更改为 Azure DNS。 使用 Azure DNS 可将 500 个主机名添加到 Web 应用。 有关详细信息，请参阅[添加子域](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)。











 


















