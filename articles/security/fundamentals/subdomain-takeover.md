---
title: 阻止子域与 Azure DNS 别名记录和 Azure App Service 的自定义域验证一起接管
description: 了解如何避免子域接管的常见高严重性威胁
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: bde4b21f9dfff62ef43afc9c9d8e5a858631d304
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447380"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>禁止无关联的 DNS 条目，并避免子域接管

本文介绍了子域接管的常见安全威胁，以及可以采取哪些措施来缓解此威胁。


## <a name="what-is-subdomain-takeover"></a>什么是子域接管？

对于定期创建和删除多个资源的组织而言，子域接管是一个常见的高严重性威胁。 如果有指向取消预配 Azure 资源的 [DNS 记录](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) ，则可能会发生子域接管。 此类 DNS 记录也称为 "无关联 DNS" 条目。 CNAME 记录特别容易受到这种威胁。 子域接管使恶意执行组件可以将组织域的预期流量重定向到执行恶意活动的站点。

子域接管的常见方案：

1. **建立**

    1. 使用完全限定的域名（ (FQDN) 预配 Azure 资源 `app-contogreat-dev-001.azurewebsites.net` 。

    1. 你可以通过将 `greatapp.contoso.com` 流量路由到 Azure 资源的子域，在 DNS 区域中分配 CNAME 记录。

1. **撤消**

    1. 在不再需要 Azure 资源后，取消预配或将其删除。 
    
        此时， `greatapp.contoso.com` *应* 从 DNS 区域中删除 CNAME 记录。 如果未删除 CNAME 记录，则会将其播发为活动域，但不会将流量路由到活动的 Azure 资源。 这是 "无关联的" DNS 记录的定义。

    1. 无关联的子域 `greatapp.contoso.com` 现在很容易受到攻击，可被分配到另一个 Azure 订阅的资源。

1. **接管**

    1. 使用常用的方法和工具，威胁参与者可发现无关联的子域。  

    1. 威胁参与者预配 Azure 资源，该资源具有以前控制的资源的相同 FQDN。 在本示例中为 `app-contogreat-dev-001.azurewebsites.net`。

    1. 发送到子域的流量 `myapp.contoso.com` 现在会路由到它们控制内容的恶意执行组件的资源。



![子域接管取消预配网站](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管的风险

当 DNS 记录指向不可用的资源时，记录本身应该已从 DNS 区域中删除。 如果它未被删除，则为 "无关联 DNS" 记录，并为子域接管提供可能。

无关联的 DNS 条目使得威胁参与者可以控制关联的 DNS 名称，以托管恶意网站或服务。 组织子域中的恶意页面和服务可能导致：

- **失去对子域内容的控制** -负按下，你的组织无法保护其内容，同时还会损坏品牌和失去信任。

- **从不受防范的访客那里收集 Cookie** -通常，web 应用会将会话 cookie 公开给子域 ( * contoso.com) ，因此任何子域都可以访问这些 cookie。 威胁参与者可以使用子域接管来构建真实的页面，欺骗用户访问其 cookie，并 (甚至) 保护 cookie。 一个常见的误解是，使用 SSL 证书可保护你的网站和你的用户的 cookie。 但是，威胁参与者可以使用劫持的子域来申请和接收有效的 SSL 证书。 有效的 SSL 证书向他们授予安全 cookie 的访问权限，并可以进一步提高恶意网站的感知合法性。

- **网页仿冒** 活动-可能在网络钓鱼活动中使用了真实外观的子域。 这适用于恶意网站和 MX 记录，这种情况下，攻击者可能会收到一封电子邮件，该电子邮件将发送到已知安全品牌的合法子域。

- **进一步的风险** -恶意网站可能用于升级到其他经典攻击，如 XSS、CSRF、CORS 绕过等。



## <a name="identify-dangling-dns-entries"></a>标识无关联的 DNS 条目

若要确定组织内可能无关联的 DNS 条目，请使用 Microsoft GitHub 托管的 PowerShell 工具 ["DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains)。

此工具可帮助 Azure 客户列出所有域，其中包含与在其订阅或租户上创建的现有 Azure 资源关联的 CNAME。

如果 Cname 位于其他 DNS 服务中并指向 Azure 资源，请在输入文件中将 Cname 提供给该工具。

该工具支持下表中列出的 Azure 资源。 该工具会提取并使用所有租户的 Cname 作为输入。


| 服务                   | 类型                                        | FQDNproperty                               | 示例                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | 属性 cName                           | `abc.azurefd.net`               |
| Azure Blob 存储        | microsoft.storage/storageaccounts           | Primaryendpoints.blob. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties。主机名                        | `abc.azureedge.net`             |
| 公共 IP 地址       | microsoft.network/publicipaddresses         | dnsSettings. fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure 流量管理器     | microsoft.network/trafficmanagerprofiles    | 属性. dnsConfig. fqdn                  | `abc.trafficmanager.net`        |
| Azure 容器实例  | microsoft.containerinstance/containergroups | ipAddress. fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API 管理      | microsoft.apimanagement/service             | hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Azure 应用服务         | microsoft.web/sites                         | defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service 槽 | microsoft.web/sites/slots                   | defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>必备条件

以具有以下权限的用户身份运行查询：

- 至少对 Azure 订阅的读者级别的访问权限
- Azure 资源关系图的读取访问权限

如果你是组织的租户的全局管理员，请使用 [提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)，使你的帐户能够访问你组织的所有订阅。


> [!TIP]
> 如果你的 Azure 环境较大，azure 资源关系图的限制和分页限制应考虑。 
> 
> [详细了解如何使用大型 Azure 资源数据集](../../governance/resource-graph/concepts/work-with-data.md)。
> 
> 该工具使用订阅批处理来避免这些限制。

### <a name="run-the-script"></a>运行脚本

详细了解 PowerShell 脚本、 **Get-DanglingDnsRecords.ps1**，并从 GitHub 下载该脚本： https://aka.ms/DanglingDNSDomains 。

## <a name="remediate-dangling-dns-entries"></a>修正无关联的 DNS 条目 

查看 DNS 区域，并识别未关联或已被占用的 CNAME 记录。 如果发现子域无关联或已被占用，请删除易受攻击的子域，并通过以下步骤降低风险：

1. 从 DNS 区域中删除所有指向已不再预配资源的 Fqdn 的 CNAME 记录。

1. 若要使流量能够路由到控件中的资源，请使用无关联的子域的 CNAME 记录中指定的 Fqdn 来预配其他资源。

1. 查看应用程序代码中特定子域的引用，并更新任何不正确或过期的子域引用。

1. 调查是否发生了任何泄露，并根据组织的事件响应过程采取措施。 有关调查此问题的提示和最佳做法，请参阅下文。

    如果你的应用程序逻辑是将机密（如 OAuth 凭据）发送到无关联的子域，或将隐私敏感信息发送到无关联的子域，则该数据可能已向第三方公开。

1. 了解在取消预配资源时未从 DNS 区域中删除 CNAME 记录的原因，并采取相应措施以确保在未来 Azure 资源取消预配时正确更新 DNS 记录。


## <a name="prevent-dangling-dns-entries"></a>禁止无关联的 DNS 条目

确保你的组织已经实现了阻止无关联 DNS 条目的过程，并且生成的子域接管是安全计划的重要组成部分。

某些 Azure 服务提供了有助于创建预防措施的功能，并对其进行了详细介绍。 其他防止此问题的方法必须通过组织的最佳实践或标准操作过程建立。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 别名记录

Azure DNS 的 [别名记录](../../dns/dns-alias.md#scenarios) 可以通过将 DNS 记录的生命周期与 Azure 资源耦合，来防止无关联的引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源，DNS 别名记录将变成空的记录集。 它不再引用已删除的资源。 需要特别注意的是，可以通过别名记录来保护的内容有限制。 目前，该列表限制为：

- Azure Front Door
- 流量管理器配置文件
- Azure 内容分发网络 (CDN) 终结点
- 公共 IP

尽管目前提供有限的服务，我们仍建议使用别名记录来防止子域接管。

[详细了解 Azure DNS 的别名记录的功能](../../dns/dns-alias.md#capabilities)。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure App Service 的自定义域验证

为 Azure App Service 创建 DNS 条目时，请创建一个 asuid。域名带有域验证 ID 的 TXT 记录。 如果存在此类 TXT 记录，则任何其他 Azure 订阅都不能验证自定义域，即接管自定义域。 

这些记录不会阻止他人创建与 CNAME 条目中的名称相同的 Azure App Service。 如果无法证明域名的所有权，威胁参与者将无法接收流量或控制内容。

[详细了解如何将现有的自定义 DNS 名称映射到 Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>构建和自动化过程以缓解威胁

通常由开发人员和运营团队运行清理进程来避免无关联的 DNS 威胁。 以下做法可帮助确保你的组织避免遭受这种威胁。 

- **创建预防过程：**

    - 使应用程序开发人员可以在删除资源时，使其重新路由。

    - 停用服务时，请在所需检查列表中放置 "删除 DNS 条目"。

    - 对具有自定义 DNS 条目的任何资源放置 [删除锁定](../../azure-resource-manager/management/lock-resources.md) 。 删除锁定用作指示符，在取消预配资源之前，必须删除该映射。 此类度量值仅在与内部教育计划结合使用时才起作用。

- **创建发现过程：**

    - 定期查看你的 DNS 记录，以确保你的子域全部都映射到以下 Azure 资源：

        - 存在-在 DNS 区域中查询指向 Azure 子域的资源，例如 *. azurewebsites.net 或 *. cloudapp.azure.com (参阅 [azure 域) 的参考列表](azure-domains.md) 。
        - 你需要确认你拥有 DNS 子域所面向的所有资源。

    - 维护 Azure 完全限定的域名的服务目录 (FQDN) 终结点和应用程序所有者。 若要生成服务目录，请运行以下 Azure 资源关系图查询脚本。 此脚本将为你有权访问的资源创建 FQDN 终结点信息，并将其输出到 CSV 文件中。 如果你有权访问租户的所有订阅，该脚本将会考虑所有这些订阅，如下面的示例脚本中所示。 若要将结果限制为一组特定的订阅，请按如下所示编辑脚本。


- **创建修补程序：**
    - 找到无关联的 DNS 条目后，你的团队需要调查是否发生了任何损害。
    - 调查停止资源后不重新路由地址的原因。
    - 如果 DNS 记录不再使用，请将其删除，或将其指向正确的 Azure 资源 (FQDN) 你的组织拥有。
 

## <a name="next-steps"></a>后续步骤

若要详细了解可用于防范子域接管的相关服务和 Azure 功能，请参阅以下页面。

- [防止无关联的 DNS 记录与 Azure DNS](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [在 Azure App Service 中添加自定义域时使用域验证 ID](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [快速入门：使用 Azure PowerShell 运行首个 Resource Graph 查询](../../governance/resource-graph/first-query-powershell.md)