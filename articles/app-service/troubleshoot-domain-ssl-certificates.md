---
title: 排查域和 SSL 证书问题 - Azure 应用服务 | Microsoft Docs
description: 排查 Azure 应用服务中的域和 SSL 证书问题
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
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 0b6bdc884107a522c81d100c0a05018cbc9d0a70
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718285"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>排查 Azure 应用服务中的域和 SSL 证书问题

本文列出了为 Azure 应用服务中的 Web 应用配置域或 SSL 证书时可能遇到的常见问题。 此外，还描述了这些问题的可能原因和解决方法。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>证书问题

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>无法将 SSL 绑定证书添加到应用 

#### <a name="symptom"></a>症状

添加 SSL 绑定时出现以下错误消息：

“未能添加 SSL 绑定。 无法设置现有 VIP 的证书，因为另一个 VIP 已使用此证书。”

#### <a name="cause"></a>原因

如果对跨多个应用的同一 IP 地址使用多个基于 IP 的 SSL 绑定，则可能会出现此问题。 例如，应用 A 具有采用旧证书的基于 IP 的 SSL。 应用 B 对同一 IP 地址使用采用新证书的基于 IP 的 SSL。 使用新证书更新应用 SSL 绑定时，此操作将会失败并出现此错误，因为同一个 IP 地址已由另一应用使用。 

#### <a name="solution"></a>解决方案 

若要解决此问题，请使用以下方法之一：

- 在应用中删除使用旧证书的基于 IP 的 SSL 绑定。 
- 新建使用新证书的基于 IP 的 SSL 绑定。

### <a name="you-cant-delete-a-certificate"></a>无法删除证书 

#### <a name="symptom"></a>症状

尝试删除证书时出现以下错误消息：

“无法删除证书，因为它当前正用于 SSL 绑定。 删除证书之前，必须删除 SSL 绑定。”

#### <a name="cause"></a>原因

如果另一个应用使用了该证书，则可能会出现此问题。

#### <a name="solution"></a>解决方案

从应用中删除该证书的 SSL 绑定。 然后尝试删除证书。 如果仍然无法删除证书，请清除 Internet 浏览器缓存，并在新浏览器窗口中重新打开 Azure 门户。 然后尝试删除证书。

### <a name="you-cant-purchase-an-app-service-certificate"></a>无法购买应用服务证书 

#### <a name="symptom"></a>症状
无法从 Azure 门户购买 [Azure 应用服务证书](./web-sites-purchase-ssl-web-site.md)。

#### <a name="cause-and-solution"></a>原因和解决方法
此问题可能是由以下任何原因导致的：

- 应用服务计划的层级为“免费”或“共享”。 这些定价层不支持 SSL。 

    **解决方案**；将应用的应用服务计划升级到“标准”。

- 订阅中没有有效的信用卡。

    **解决方案**；将有效的信用卡添加到订阅。 

- 该订阅套餐不支持购买“Microsoft 学生”等应用服务证书。  

    **解决方案**；升级订阅。 

- 订阅已达到允许的购买限制。

    **解决方案**；对于即用即付和 EA 订阅类型，可购买的应用服务证书限制为 10 个。 对于其他订阅类型，限制为 3 个。 若要提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 应用服务证书标记为欺诈。 收到以下错误消息：“你的证书已被标记为可能存在欺诈。 请求当前正在审查中。 如果证书不会不能使用 24 小时内，请联系 Azure 支持。"

    **解决方案**；如果证书标记为欺诈，并且在 24 小时后未得到解决，请执行以下步骤：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 转到“应用服务证书”，选择该证书。 
    3. 选择“证书配置”   > “步骤 2:  验证” > “域验证”  。 此步骤会向 Azure 证书提供者发送一份电子邮件通知，让他们解决问题。

## <a name="custom-domain-problems"></a>自定义域问题

### <a name="a-custom-domain-returns-a-404-error"></a>自定义域返回 404 错误 

#### <a name="symptom"></a>症状

使用自定义域名浏览到站点时，收到以下错误消息：

“错误 404 - 找不到 Web 应用”。

#### <a name="cause-and-solution"></a>原因和解决方法

**原因 1** 

配置的自定义域缺少 CNAME 或 A 记录。 

**原因 1 的解决方法**

- 如果添加了 A 记录，请确保同时添加 TXT 记录。 有关详细信息，请参阅[创建 A 记录](./app-service-web-tutorial-custom-domain.md#create-the-a-record)。
- 如果不需要对应用使用根域，我们建议使用 CNAME 记录，而不要使用 A 记录。
- 不要对同一个域同时使用 CNAME 记录和 A 记录。 此问题可能会导致冲突，并阻止域解析。 

**原因 2** 

Internet 浏览器可能仍在缓存域的旧 IP 地址。 

**原因 2 的解决方法**

清除浏览器缓存。 对于 Windows 设备，可以运行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 验证域是否指向应用的 IP 地址。 

### <a name="you-cant-add-a-subdomain"></a>无法添加子域 

#### <a name="symptom"></a>症状

无法将新主机名添加到应用，因此无法分配子域。

#### <a name="solution"></a>解决方案

- 咨询订阅管理员，确保有权将主机名添加到应用。
- 如果需要更多子域，我们建议将域托管服务更改为 Azure 域服务 (DNS)。 使用 Azure DNS 可将 500 个主机名添加到应用。 有关详细信息，请参阅[添加子域](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)。

### <a name="dns-cant-be-resolved"></a>无法解析 DNS

#### <a name="symptom"></a>症状

收到以下错误消息：

“找不到 DNS 记录。”

#### <a name="cause"></a>原因
此问题是由以下原因之一导致的：

- 生存期 (TTL) 未过。 检查域的 DNS 配置以确定 TTL 值，然后等到期限已过。
- DNS 配置不正确。

#### <a name="solution"></a>解决方案
- 等待 48 小时，让此问题自行解决。
- 如果可以在 DNS 配置中更改 TTL 设置，请将值更改为 5 分钟，然后看看是否能解决问题。
- 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 验证域是否指向应用的 IP 地址。 如果不是，请将 A 记录配置为应用的正确 IP 地址。

### <a name="you-need-to-restore-a-deleted-domain"></a>需要还原已删除的域 

#### <a name="symptom"></a>症状
域不再显示在 Azure 门户中。

#### <a name="cause"></a>原因 
订阅所有者可能意外删除了该域。

#### <a name="solution"></a>解决方案
如果域的删除时间不超过七天，则尚未对该域启动删除过程。 在这种情况下，可以在 Azure 门户中的同一个订阅下购买同一个域。 （请务必在搜索框中键入确切的域名。）此域不会重复产生费用。 如果该域的删除时间超过七天，请求助 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来还原该域。

## <a name="domain-problems"></a>域问题

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>为错误的域购买了 SSL 证书

#### <a name="symptom"></a>症状

为错误的域购买了应用服务证书。 并且无法将该证书更新为使用正确的域。

#### <a name="solution"></a>解决方案

删除该证书，然后购买新证书。

如果使用错误域的当前证书处于“已颁发”状态，则该证书也会产生费用。 应用服务证书不可退款，但你可以联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，看看是否还有其他解决办法。 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>应用服务证书已续订，但应用显示旧证书 

#### <a name="symptom"></a>症状

应用服务证书已续订，但使用应用服务证书的应用仍在使用旧证书。 此外，出现需要 HTTPS 协议的警告。

#### <a name="cause"></a>原因 
Azure 应用服务每隔 8 小时会运行一个后台作业，如果有任何更改发生，则会同步证书资源。 在轮换或更新证书时，应用程序有时仍会检索旧证书，而不是最近更新的证书。 原因是同步证书资源的作业尚未运行。 
 
#### <a name="solution"></a>解决方案

可以强制同步证书：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“应用服务证书”，然后选择该证书。 
2. 依次单击“重新生成密钥和同步”、“同步”。   同步过程需要一段时间才能完成。 
3. 同步完成后，将看到以下通知：“已成功使用最新的证书更新了所有资源。”

### <a name="domain-verification-is-not-working"></a>域验证无法进行 

#### <a name="symptom"></a>症状 
应用服务证书要求先经过域验证，然后该证书才可供使用。 选择“验证”时，验证过程失败。 

#### <a name="solution"></a>解决方案
通过添加 TXT 记录来手动验证域：
 
1.  转到托管域名的域名服务 (DNS) 提供商站点。
2.  添加域的 TXT 记录，并在其中使用 Azure 门户中显示的域令牌值。 

等待几分钟以运行 DNS 传播，然后选择“刷新”按钮触发验证。  

另一种做法是使用 HTML 网页方法来手动验证域。 此方法可让证书颁发机构确认为其颁发证书的域的域所有权。

1.  创建名为 {Domain Verification Token}.html 的 HTML 文件。 此文件的内容应为域验证令牌的值。
3.  将此文件上传到托管域的 Web 服务器的根目录。
4.  选择“刷新”检查证书状态。  验证可能需要几分钟才能完成。

例如，如果为 azure.com 购买了域验证令牌为 1234abcd 的标准证书，则对 https://azure.com/1234abcd.html 发出的 Web 请求应返回 1234abcd。 

> [!IMPORTANT]
> 下达证书订单后，只有 15 天时间用于完成域验证操作。 15 天过后，证书颁发机构会拒绝证书，但该证书不会产生费用。 在此情况下，请删除该证书并重试。
>
> 

### <a name="you-cant-purchase-a-domain"></a>无法购买某个域

#### <a name="symptom"></a>症状
不能在 Azure 门户中购买应用服务域。

#### <a name="cause-and-solution"></a>原因和解决方法

此问题是由以下原因之一导致的：

- Azure 订阅中没有信用卡，或信用卡无效。

    **解决方案**；将有效的信用卡添加到订阅。

- 你不是订阅所有者，因此无权购买域。

    **解决方案**；[将“所有者”角色分配](../role-based-access-control/role-assignments-portal.md)给自己的帐户。 或者联系订阅管理员获取购买域的权限。
- 已达到订阅中可购买域数的限制。 当前限制为 20 个。

    **解决方案**；若要请求提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- Azure 订阅类型不支持购买应用服务域。

    **解决方案**；将 Azure 订阅升级到其他订阅类型，例如即用即付订阅。

### <a name="you-cant-add-a-host-name-to-an-app"></a>无法将主机名添加到应用 

#### <a name="symptom"></a>症状

在添加主机名的过程中无法验证域。

#### <a name="cause"></a>原因 

此问题是由以下原因之一导致的：

- 无权添加主机名。

    **解决方案**；让订阅管理员为你授予添加主机名的权限。
- 无法验证域所有权。

    **解决方案**；验证是否已正确配置 CNAME 或 A 记录。 若要将自定义域映射到应用，请创建 CNAME 记录或 A 记录。 若要使用根域，必须使用 A 和 TXT 记录：

    |记录类型|主机|指向|
    |------|------|-----|
    |A|@|应用的 IP 地址|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>常见问题解答

**购买网站的自定义域后是否必须配置该域？**

通过 Azure 门户购买某个域时，应用服务应用程序会自动配置为使用该自定义域。 你不需要执行任何额外的步骤。 有关详细信息，观看[Azure 应用服务自助帮助：自定义域名添加](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)第 9 频道上。

**是否可以使用在 Azure 门户中购买的域来指向 Azure VM？**

是的您可以将域指向 VM 了。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](../dns/dns-custom-domain.md)。

**我的域是由 GoDaddy 还是 Azure DNS 托管？**

应用服务域使用 GoDaddy 进行域注册，使用 Azure DNS 来托管域。 

**我已启用自动续订，但仍收到了有关域续订的电子邮件通知。我该怎样做？**

如果你已启用自动续订，则不需要执行任何操作。 电子邮件通知旨在告诉你该域即将过期，如果未启用自动续订，则需要手动续订。

**在 Azure DNS 中托管域是否要付费？**

最初的域购买费用仅适用于域注册。 除了注册费用以外，Azure DNS 还会根据用量收费。 有关详细详细，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

**我的域是之前在 Azure 门户中购买的，现在想要从 GoDaddy 托管改为 Azure DNS 托管。该如何处理？**

不一定非要迁移到 Azure DNS 托管。 如果你确实想要迁移到 Azure DNS，Azure 门户中的域管理体验会提供有关转移到 Azure DNS 的步骤信息。 如果域通过应用服务购买的，则从 GoDaddy 托管迁移到 Azure DNS 的过程相对较为顺畅。

**如果通过应用服务域购买域，是否可以在 GoDaddy 而不是 Azure DNS 中托管该域？**

从 2017 年 7 月 24 日开始，在门户中购买的应用服务域将托管在 Azure DNS 中。 如果你想要使用其他托管提供商，则必须转到其网站以获取域托管解决方案。

**是否需要支付域的隐私保护费用？**

通过 Azure 门户购买域时，可以选择免费添加隐私保护。 这是通过 Azure 应用服务购买域所能获得的权益之一。

**如果我不再想要使用我的域，是否可以获得退款？**

购买域时，你可以免费试用 5 天，在此期间，可以决定是否要继续使用。 如果在这五天内你决定不需要该域，则不会产生费用。 （.uk 域例外。 购买 .uk 域会立即产生费用，而不能获得退款）。

**是否可以在订阅中的另一个 Azure 应用服务应用中使用域？**

是的。 在 Azure 门户中访问“自定义域和 SSL”边栏选项卡时，会看到购买的域。 可将应用配置为使用其中的任何域。

**是否可将域从一个订阅转移到另一个订阅？**

可以使用 [Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell cmdlet 将域转移到另一个订阅/资源组。

**如果我当前没有 Azure 应用服务应用，该如何管理自定义域？**

即使没有应用服务 Web 应用，也可以管理域。 域可用于虚拟机、存储等 Azure 服务。如果你打算将域用于应用服务 Web 应用，则需要添加一个未包含在免费应用服务计划中的 Web 应用才能将域绑定到 Web 应用。

**是否可将使用自定义域的 Web 应用移到另一个订阅，或者将其从应用服务环境 v1 移到 v2？**

是的，可以在订阅之间移动 Web 应用。 请遵照[如何在 Azure 中移动资源](../azure-resource-manager/resource-group-move-resources.md)中的指导操作。 移动 Web 应用时存在一些限制。 有关详细信息，请参阅[移动应用服务资源时存在的限制](../azure-resource-manager/move-limitations/app-service-move-limitations.md)。

移动 Web 应用之后，自定义域设置中的域的主机名绑定应保持不变。 无需执行额外的步骤即可配置主机名绑定。
