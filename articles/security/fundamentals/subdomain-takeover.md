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
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 2baf2b209cae11f734494c377aebd731f69f514d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610857"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>禁止无关联的 DNS 条目，并避免子域接管

本文介绍了子域接管的常见安全威胁，以及可以采取哪些措施来缓解此威胁。


## <a name="what-is-subdomain-takeover"></a>什么是子域接管？

对于定期创建和删除多个资源的组织而言，子域接管是一个常见的高严重性威胁。 如果有指向取消预配 Azure 资源的 DNS 记录，则可能会发生子域接管。 此类 DNS 记录也称为 "无关联 DNS" 条目。 CNAME 记录特别容易受到这种威胁。

子域接管的常见方案：

1. 将创建一个网站。 

    在本示例中为 `app-contogreat-dev-001.azurewebsites.net`。

1. 一个 CNAME 条目将添加到指向该网站的 DNS。 

    在此示例中，创建了以下友好名称： `greatapp.contoso.com` 。

1. 几个月后，将不再需要该站点，因此将其删除，**而不会**删除相应的 DNS 条目。 

    CNAME DNS 条目现在为 "无关联"。

1. 几乎在站点被删除后立刻立即发现丢失的站点，然后在上创建他们自己的网站 `app-contogreat-dev-001.azurewebsites.net` 。

    现在，流量旨在 `greatapp.contoso.com` 发送到威胁参与者的 Azure 站点，威胁参与者正在控制显示的内容。 

    利用无关联 DNS，Contoso 的子域 "GreatApp" 已成为子域接管的受害者。 

![子域接管取消预配网站](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管的风险

当 DNS 记录指向不可用的资源时，记录本身应该已从 DNS 区域中删除。 如果它未被删除，则为 "无关联 DNS" 记录，并为子域接管提供可能。

无关联的 DNS 条目使得威胁参与者可以控制关联的 DNS 名称，以托管恶意网站或服务。 组织子域中的恶意页面和服务可能导致：

- **失去对子域内容的控制**-负按下，你的组织无法保护其内容，同时还会损坏品牌和失去信任。

- **从不受防范的访客那里收集 cookie** -通常，web 应用会将会话 cookie 公开给子域（*. contoso.com），因此任何子域都可以访问这些 cookie。 威胁参与者可以使用子域接管来构建真实的页面，欺骗用户访问它，并搜集其 cookie （甚至是安全 cookie）。 一个常见的误解是，使用 SSL 证书可保护你的网站和你的用户的 cookie。 但是，威胁参与者可以使用劫持的子域来申请和接收有效的 SSL 证书。 然后，这会授予他们访问安全 cookie 的权限，并可以进一步增加恶意网站的感知合法性。

- **网络钓鱼活动**-可在网络钓鱼活动中使用可信外观的子域。 这适用于恶意站点以及 MX 记录，这种情况下，攻击者可能会收到发送到已知安全品牌合法子域的电子邮件。

- **进一步的风险**-恶意网站可用于升级到其他经典攻击，如 XSS、CSRF、CORS 回避等。



## <a name="preventing-dangling-dns-entries"></a>禁止无关联的 DNS 条目

确保你的组织已经实现了阻止无关联 DNS 条目的过程，并且生成的子域接管是安全计划的重要组成部分。

下面列出了你目前可用的预防措施。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 别名记录

通过与 Azure 资源紧密耦合 DNS 记录的生命周期，Azure DNS 的[别名记录](https://docs.microsoft.com/azure/dns/dns-alias#scenarios)可防止无关联引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源，DNS 别名记录将变成空的记录集。 它不再引用已删除的资源。 需要特别注意的是，可以通过别名记录来保护的内容有限制。 目前，该列表限制为：

- Azure Front Door
- 流量管理器配置文件
- Azure 内容分发网络（CDN）终结点
- 公共 IP

如果你的资源可以使用别名记录进行保护，则建议你不要这样做。

[详细了解](https://docs.microsoft.com/azure/dns/dns-alias#capabilities)Azure DNS 的别名记录的功能。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure App Service 的自定义域验证

为 Azure App Service 创建 DNS 条目时，请创建一个 asuid。域名带有域验证 ID 的 TXT 记录。 如果存在此类 TXT 记录，则任何其他 Azure 订阅都不能验证自定义域，即接管自定义域。 

这些记录不会阻止他人创建与 CNAME 条目中的名称相同的 Azure App Service。 如果无法证明域名的所有权，威胁参与者将无法接收流量或控制内容。

[详细了解](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain)如何将现有的自定义 DNS 名称映射到 Azure App Service。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>构建和自动化过程以缓解威胁

通常由开发人员和运营团队运行清理进程来避免无关联的 DNS 威胁。 以下做法可帮助确保你的组织避免遭受这种威胁。 

- **创建预防过程：**

    - 使应用程序开发人员可以在删除资源时，使其重新路由。

    - 停用服务时，请在所需检查列表中放置 "删除 DNS 条目"。

    - 对具有自定义 DNS 条目的任何资源放置[删除锁定](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 删除锁定用作指示符，在取消预配资源之前，必须删除该映射。 此类度量值仅在与内部教育计划结合使用时才起作用。

- **创建发现过程：**

    - 定期查看你的 DNS 记录，以确保你的子域全部都映射到以下 Azure 资源：

        - **存在**-在 DNS 区域中查询指向 Azure 子域的资源，例如 *. azurewebsites.net 或 cloudapp.azure.com （请参阅[此引用列表](azure-domains.md)）。
        - **你需要**确认你拥有 DNS 子域所面向的所有资源。

    - 维护 Azure 完全限定的域名（FQDN）终结点和应用程序所有者的服务目录。 若要生成你的服务目录，请运行以下 Azure 资源 Graph 查询，其中包含下表中的参数：
    
        >[!IMPORTANT]
        > **权限**-以有权访问所有 Azure 订阅的用户身份运行查询。 
        >
        > **限制**-Azure 资源图的限制和分页限制，你应考虑使用大型 azure 环境。 [详细了解](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data)如何使用大型 Azure 资源数据集。  

        ```
        Search-AzGraph -Query "resources | where type == '[ResourceType]' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = [FQDNproperty]"
        ``` 
        
        例如，以下查询将返回 Azure App Service 中的资源：

        ```
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```
        
        还可以合并多个资源类型。 此示例查询返回 Azure App Service**和**Azure App Service 槽中的资源：

        ```
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```

        |资源名称  |[ResourceType]  | [FQDNproperty]  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|属性 cName|
        |Azure Blob 存储|microsoft.storage/storageaccounts|Primaryendpoints.blob. blob|
        |Azure CDN|microsoft.cdn/profiles/endpoints|properties。主机名|
        |公共 IP 地址|microsoft.network/publicipaddresses|dnsSettings. fqdn|
        |Azure 流量管理器|microsoft.network/trafficmanagerprofiles|属性. dnsConfig. fqdn|
        |Azure 容器实例|microsoft.containerinstance/containergroups|ipAddress. fqdn|
        |Azure API 管理|microsoft.apimanagement/service|hostnameConfigurations. hostName|
        |Azure 应用服务|microsoft.web/sites|defaultHostName|
        |Azure App Service 槽|microsoft.web/sites/slots|defaultHostName|


- **创建修补程序：**
    - 找到无关联的 DNS 条目后，你的团队需要调查是否发生了任何损害。
    - 调查停止资源后不重新路由地址的原因。
    - 删除不再使用的 DNS 记录，或将其指向组织拥有的正确 Azure 资源（FQDN）。
 

## <a name="next-steps"></a>后续步骤

若要详细了解可用于防范子域接管的相关服务和 Azure 功能，请参阅以下页面。

- [Azure DNS 支持对自定义域使用别名记录](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [在 Azure App Service 中添加自定义域时使用域验证 ID](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

-    [快速入门：使用 Azure PowerShell 运行首个 Resource Graph 查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)