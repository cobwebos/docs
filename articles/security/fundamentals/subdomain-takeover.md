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
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890818"
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

1. 几个月后，将不再需要该站点，因此将其删除， **而不会** 删除相应的 DNS 条目。 

    CNAME DNS 条目现在为 "无关联"。

1. 几乎在站点被删除后立刻立即发现丢失的站点，然后在上创建他们自己的网站 `app-contogreat-dev-001.azurewebsites.net` 。

    现在，流量旨在 `greatapp.contoso.com` 发送到威胁参与者的 Azure 站点，威胁参与者控制所显示的内容。 

    利用无关联 DNS，Contoso 的子域 "GreatApp" 已成为子域接管的受害者。 

![子域接管取消预配网站](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管的风险

当 DNS 记录指向不可用的资源时，记录本身应该已从 DNS 区域中删除。 如果它未被删除，则为 "无关联 DNS" 记录，并为子域接管提供可能。

无关联的 DNS 条目使得威胁参与者可以控制关联的 DNS 名称，以托管恶意网站或服务。 组织子域中的恶意页面和服务可能导致：

- **失去对子域内容的控制** -负按下，你的组织无法保护其内容，同时还会损坏品牌和失去信任。

- **从不受防范的访客那里收集 Cookie** -通常，web 应用会将会话 cookie 公开给子域 ( * contoso.com) ，因此任何子域都可以访问这些 cookie。 威胁参与者可以使用子域接管来构建真实的页面，欺骗用户访问其 cookie，并 (甚至) 保护 cookie。 一个常见的误解是，使用 SSL 证书可保护你的网站和你的用户的 cookie。 但是，威胁参与者可以使用劫持的子域来申请和接收有效的 SSL 证书。 有效的 SSL 证书向他们授予安全 cookie 的访问权限，并可以进一步提高恶意网站的感知合法性。

- **网络钓鱼活动** -可在网络钓鱼活动中使用可信外观的子域。 这适用于恶意站点以及 MX 记录，这种情况下，攻击者可能会收到发送到已知安全品牌合法子域的电子邮件。

- **进一步的风险** -恶意网站可用于升级到其他经典攻击，如 XSS、CSRF、CORS 回避等。



## <a name="preventing-dangling-dns-entries"></a>禁止无关联的 DNS 条目

确保你的组织已经实现了阻止无关联 DNS 条目的过程，并且生成的子域接管是安全计划的重要组成部分。

下面列出了你目前可用的预防措施。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 别名记录

Azure DNS 的 [别名记录](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) 可以通过将 DNS 记录的生命周期与 Azure 资源耦合，来防止无关联的引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源，DNS 别名记录将变成空的记录集。 它不再引用已删除的资源。 需要特别注意的是，可以通过别名记录来保护的内容有限制。 目前，该列表限制为：

- Azure Front Door
- 流量管理器配置文件
- Azure 内容分发网络 (CDN) 终结点
- 公共 IP

尽管目前提供有限的服务，我们仍建议使用别名记录来防止子域接管。

[详细了解](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) Azure DNS 的别名记录的功能。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure App Service 的自定义域验证

为 Azure App Service 创建 DNS 条目时，请创建一个 asuid。域名带有域验证 ID 的 TXT 记录。 如果存在此类 TXT 记录，则任何其他 Azure 订阅都不能验证自定义域，即接管自定义域。 

这些记录不会阻止他人创建与 CNAME 条目中的名称相同的 Azure App Service。 如果无法证明域名的所有权，威胁参与者将无法接收流量或控制内容。

[详细了解](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) 如何将现有的自定义 DNS 名称映射到 Azure App Service。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>构建和自动化过程以缓解威胁

通常由开发人员和运营团队运行清理进程来避免无关联的 DNS 威胁。 以下做法可帮助确保你的组织避免遭受这种威胁。 

- **创建预防过程：**

    - 使应用程序开发人员可以在删除资源时，使其重新路由。

    - 停用服务时，请在所需检查列表中放置 "删除 DNS 条目"。

    - 对具有自定义 DNS 条目的任何资源放置 [删除锁定](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) 。 删除锁定用作指示符，在取消预配资源之前，必须删除该映射。 此类度量值仅在与内部教育计划结合使用时才起作用。

- **创建发现过程：**

    - 定期查看你的 DNS 记录，以确保你的子域全部都映射到以下 Azure 资源：

        - 存在-在 DNS 区域中查询指向 Azure 子域的资源，例如 *. azurewebsites.net 或 *. cloudapp.azure.com (参阅 [此引用列表](azure-domains.md)) 。
        - 你需要确认你拥有 DNS 子域所面向的所有资源。

    - 维护 Azure 完全限定的域名的服务目录 (FQDN) 终结点和应用程序所有者。 若要生成服务目录，请运行以下 Azure 资源关系图查询脚本。 此脚本将为你有权访问的资源创建 FQDN 终结点信息，并将其输出到 CSV 文件中。 如果你有权访问租户的所有订阅，该脚本将会考虑所有这些订阅，如下面的示例脚本中所示。 若要将结果限制为一组特定的订阅，请按如下所示编辑脚本。

        >[!IMPORTANT]
        > **权限** -以有权访问所有 Azure 订阅的用户身份运行查询。 
        >
        > **限制** -Azure 资源图的限制和分页限制，你应考虑使用大型 azure 环境。 [详细了解](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) 如何使用大型 Azure 资源数据集。 下面的示例脚本使用订阅批处理来避免这些限制。

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        `FQDNProperty`前面的资源关系图查询中指定的类型及其值的列表：

        |资源名称  | `<ResourceType>`  | `<FQDNproperty>`  |
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
    - 如果 DNS 记录不再使用，请将其删除，或将其指向正确的 Azure 资源 (FQDN) 你的组织拥有。
 

## <a name="next-steps"></a>后续步骤

若要详细了解可用于防范子域接管的相关服务和 Azure 功能，请参阅以下页面。

- [Azure DNS 支持对自定义域使用别名记录](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [在 Azure App Service 中添加自定义域时使用域验证 ID](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [快速入门：使用 Azure PowerShell 运行首个 Resource Graph 查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
