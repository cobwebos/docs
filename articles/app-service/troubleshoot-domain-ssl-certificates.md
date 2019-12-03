---
title: 域和 SSL 证书疑难解答
description: 查找在 Azure App Service 中配置域或 SSL 证书时可能遇到的常见问题的解决方案。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 2260dddd74d7ed64eb19158a5360ed2e4c09b4a9
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688340"
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
无法从 Azure 门户购买 [Azure 应用服务证书](./configure-ssl-certificate.md#import-an-app-service-certificate)。

#### <a name="cause-and-solution"></a>原因和解决方法
此问题可能是由以下任何原因导致的：

- 应用服务计划的层级为“免费”或“共享”。 这些定价层不支持 SSL。 

    **解决方案**：将应用程序的应用服务计划升级到 Standard。

- 订阅中没有有效的信用卡。

    **解决方法**：将有效的信用卡添加到订阅。 

- 该订阅套餐不支持购买“Microsoft 学生”等应用服务证书。  

    **解决方法**：升级订阅。 

- 订阅已达到允许的购买限制。

    **解决方法**：对于即用即付和 EA 订阅类型，可购买的应用服务证书限制为 10 个。 对于其他订阅类型，限制为 3 个。 若要提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 应用服务证书标记为欺诈。 收到以下错误消息：“证书已被标记为可能存在欺诈。 请求当前正在审查中。 如果在24小时内证书不会变得可用，请联系 Azure 支持。 "

    **解决方法**：如果证书标记为欺诈，并且在 24 小时后未得到解决，请遵循以下步骤：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 转到“应用服务证书”，选择该证书。
    3. 选择“证书配置” > “步骤 2: 验证” > “域验证”。 此步骤会向 Azure 证书提供者发送一份电子邮件通知，让他们解决问题。

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
- 不要对同一个域同时使用 CNAME 记录和 A 记录。 此问题可能会导致冲突并防止域被解析。 

**原因 2** 

Internet 浏览器可能仍在缓存域的旧 IP 地址。 

**原因 2 的解决方法**

清除浏览器缓存。 对于 Windows 设备，可以运行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 验证域是否指向应用的 IP 地址。 

### <a name="you-cant-add-a-subdomain"></a>无法添加子域 

#### <a name="symptom"></a>症状

无法将新主机名添加到应用，因此无法分配子域。

#### <a name="solution"></a>解决方案

- 咨询订阅管理员，确保有权将主机名添加到应用。
- 如果需要更多的子域，建议你将托管的域更改为 Azure 域名服务（DNS）。 使用 Azure DNS 可将 500 个主机名添加到应用。 有关详细信息，请参阅[添加子域](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)。

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
如果域的删除时间不超过七天，则尚未对该域启动删除过程。 在这种情况下，可以在 Azure 门户中的同一个订阅下购买同一个域。 （请确保在搜索框中键入准确的域名。）此域不会再为你收费。 如果域以前被删除了7天以上，请联系[Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门以获取有关还原域的帮助。

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
应用服务会在48小时内自动同步证书。 在轮换或更新证书时，应用程序有时仍会检索旧证书，而不是最近更新的证书。 原因是同步证书资源的作业尚未运行。 单击 "同步"。同步操作会自动更新应用服务中证书的主机名绑定，而不会导致应用程序停机。
 
#### <a name="solution"></a>解决方案

可以强制同步证书：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“应用服务证书”，然后选择该证书。
2. 选择 " **Rekey 和同步**"，然后选择 "**同步**"。同步需要一些时间才能完成。 
3. 完成同步后，会看到以下通知：“已成功使用最新的证书更新了所有资源”。

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
4.  选择“刷新”检查证书状态。 验证可能需要几分钟才能完成。

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

    **解决方法**：将有效的信用卡添加到订阅。

- 你不是订阅所有者，因此无权购买域。

    **解决方法**：[将“所有者”角色分配](../role-based-access-control/role-assignments-portal.md)给自己的帐户。 或者联系订阅管理员获取购买域的权限。
- 已达到订阅中可购买域数的限制。 当前限制为 20 个。

    **解决方法**：若要请求提高限制，请联系 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- Azure 订阅类型不支持购买应用服务域。

    **解决方法**：将 Azure 订阅升级到其他订阅类型，例如即用即付订阅。

### <a name="you-cant-add-a-host-name-to-an-app"></a>无法将主机名添加到应用 

#### <a name="symptom"></a>症状

在添加主机名的过程中无法验证域。

#### <a name="cause"></a>原因 

此问题是由以下原因之一导致的：

- 无权添加主机名。

    **解决方法**：让订阅管理员为你授予添加主机名的权限。
- 无法验证域所有权。

    **解决方法**：验证是否已正确配置 CNAME 或 A 记录。 若要将自定义域映射到应用，请创建 CNAME 记录或 A 记录。 若要使用根域，必须使用 A 和 TXT 记录：

    |记录类型|主机|指向|
    |------|------|-----|
    |A|@|应用的 IP 地址|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>常见问题解答

**购买我的网站后，是否需要为我的网站配置我的自定义域？**

从 Azure 门户购买域时，应用服务应用程序将自动配置为使用该自定义域。 无需执行任何其他步骤。 有关详细信息，请观看[Azure App Service 自助：在 Channel9 上添加自定义域名](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)。

**是否可以使用在 Azure 门户中购买的域来改为指向 Azure VM？**

是的，你可以将域指向 VM。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](../dns/dns-custom-domain.md)。

**我的域是由 GoDaddy 还是 Azure DNS 托管？**

应用服务域使用 GoDaddy 进行域注册，并 Azure DNS 来托管域。 

**我已启用自动续订，但仍通过电子邮件收到域的续订通知。我该怎么办？**

如果已启用自动续订，则无需执行任何操作。 提供了通知电子邮件，通知你域接近过期，如果未启用自动续订，则手动续订。

**是否需要为域托管 Azure DNS 收费？**

域购买的初始成本仅适用于域注册。 除注册开销外，还会根据你的使用情况产生 Azure DNS 的费用。 有关详细信息，请参阅[Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)了解更多详细信息。

**我之前从 Azure 门户购买了域，并希望从 GoDaddy 托管到 Azure DNS 托管。如何执行此操作？**

迁移到 Azure DNS 托管并不是必需的。 如果你确实想要迁移到 Azure DNS，Azure 门户中的域管理体验提供了有关迁移到 Azure DNS 所需步骤的信息。 如果域是通过应用服务购买的，则从 GoDaddy 托管到 Azure DNS 的迁移是相对无缝的过程。

**我想从应用服务域购买我的域，但可以在 GoDaddy 上（而不是 Azure DNS）托管域？**

从2017年7月24日开始，在门户中购买的应用服务域托管在 Azure DNS 上。 如果希望使用不同的托管提供商，则必须访问其网站以获取域托管解决方案。

**我是否需要为域的隐私保护付费？**

通过 Azure 门户购买域时，可以选择添加隐私，无需额外付费。 这是通过 Azure App Service 购买域的优点之一。

**如果我决定不再需要我的域，是否可以恢复我的资金？**

当你购买某个域时，你不需要支付5天的费用，在这段时间内，你可以决定不需要该域。 如果你确定不希望该域处于5天内，则不会向你收费。 （英国域是此情况的例外情况。 如果购买的是英国域，则会立即向你收费，并且你无法退还。）

**能否在我的订阅中的另一个 Azure App Service 应用中使用该域？**

可以。 在 Azure 门户中访问 "自定义域和 SSL" 边栏选项卡时，将看到已购买的域。 你可以将应用程序配置为使用这些域中的任何一个。

**是否可以将一个订阅中的域转移到另一个订阅？**

可以使用[AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell cmdlet 将域移到另一个订阅/资源组。

**如果当前没有 Azure App Service 应用，如何管理我的自定义域？**

即使没有应用服务 Web 应用，也可以管理域。 域可用于 Azure 服务，例如虚拟机、存储等。如果要将域用于应用服务 Web 应用，则需要包含不在免费应用服务计划中的 Web 应用，以便将域绑定到你的 Web 应用。

**是否可以将包含自定义域的 web 应用移到另一个订阅或从应用服务环境 v1 迁移到 V2？**

是的，你可以跨订阅移动 web 应用。 按照[如何在 Azure 中移动资源](../azure-resource-manager/resource-group-move-resources.md)中的指导进行操作。 移动 web 应用时有一些限制。 有关详细信息，请参阅[移动应用服务资源的限制](../azure-resource-manager/move-limitations/app-service-move-limitations.md)。

移动 web 应用后，自定义域中的域的主机名绑定应保持不变。 配置主机名绑定不需要执行其他步骤。
