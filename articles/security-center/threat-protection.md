---
title: Azure 安全中心的威胁防护
description: 本主题介绍由 Azure 安全中心的威胁防护功能保护的资源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: b28901918f2606100d92f47800c6e0fb6778e3d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82606885"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 安全中心的威胁防护

当安全中心检测到环境中任何区域的威胁时，会生成警报。 这些警报描述了受影响资源的详细信息、建议的修正步骤，以及在某些情况下用于触发逻辑应用响应的选项。

Azure 安全中心的威胁防护为你的环境提供了综合防御：

* **Azure 计算资源的威胁防护**： Windows 计算机、Linux 计算机、Azure App Service 和 Azure 容器

* **Azure 数据资源的威胁防护**： sql 数据库和 Sql 数据仓库、Azure 存储和 Azure Cosmos DB

* **Azure 服务层的威胁防护**： azure 网络层、azure 管理层（azure 资源管理器）（预览版）和 Azure Key Vault （预览版）

无论警报是由安全中心生成，还是由其他安全产品的安全中心接收，你都可以将其导出。 若要将警报导出到 Azure Sentinel （或第三方 SIEM）或任何其他外部工具，请按照将[警报导出到 SIEM](continuous-export.md)中的说明进行操作。 

> [!TIP]
> 若要启用安全中心的威胁防护功能，必须将标准定价层应用到包含适用工作负荷的订阅。
>
> 可以在订阅级别或资源级别为**Azure 存储帐户**启用威胁防护。
> 可以在订阅级别或资源级别为**AZURE Sql 数据库 sql server**启用威胁防护。
> 只能在资源级别为**Azure Database for MariaDB/MySQL/PostgreSQL**启用威胁防护。



## <a name="threat-protection-for-windows-machines"></a>Windows 计算机的威胁防护<a name="windows-machines"></a>

Azure 安全中心与 Azure 服务集成，以监视和保护基于 Windows 的计算机。 安全中心以一种易于使用的格式从所有这些服务提供警报和修正建议。

* **Microsoft defender ATP** <a name="windows-atp"></a> -安全中心通过与 Microsoft defender 高级威胁防护（ATP）集成来扩展其云工作负荷保护平台。 它们共同提供了全面的终结点检测和响应（EDR）功能。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 服务器上会自动启用 Microsoft Defender ATP 传感器。

    当 Microsoft Defender ATP 检测到威胁时，会触发警报。 警报显示在安全中心仪表板上。 在仪表板中，可以透视到 Microsoft Defender ATP 控制台，并执行详细的调查以发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅将[服务器载入 Microsoft DEFENDER atp 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **故障转储分析** <a name="windows-dump"></a> -当软件崩溃时，崩溃转储在崩溃时捕获部分内存。

    故障可能是恶意软件或包含恶意软件造成的。 为避免被安全产品检测到，各种形式的恶意软件使用 fileless 攻击，这可避免写入磁盘或加密写入磁盘的软件组件。 使用传统的基于磁盘的方法很难检测到这种类型的攻击。

    但是，通过使用内存分析，可以检测到这种攻击。 通过分析故障转储中的内存，安全中心可以检测到攻击所使用的技术。 例如，攻击可能试图利用软件中的漏洞、访问机密数据以及偷偷在受损的计算机中保持不变。 安全中心执行此操作时，对主机性能的影响最小。

    有关故障转储分析警报的详细信息，请参阅[警报的引用表](alerts-reference.md#alerts-windows)。

* **Fileless 攻击检测** <a name="windows-fileless"></a> -Fileless 攻击定向到终结点很常见。 为了避免检测，fileless 攻击会将恶意有效负载注入内存。 攻击者负载会在遭到入侵的进程中保持不变，并执行各种恶意活动。

    通过 fileless 攻击检测，自动内存鉴证技术识别 fileless 攻击工具包、技术和行为。 此解决方案会定期在运行时扫描计算机，并直接从安全关键进程的内存中提取见解。

    它找到了利用、代码注入和恶意负载执行的证据。 Fileless 攻击检测生成详细的安全警报，以加速警报会审、相关和下游响应时间。 此方法补充基于事件的 EDR 解决方案，提供更高的检测范围。

    有关 fileless 攻击检测警报的详细信息，请参阅[警报的引用表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 可以通过下载[Azure 安全中心操作手册：安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)来模拟 Windows 警报。






## <a name="threat-protection-for-linux-machines"></a>适用于 Linux 计算机的威胁防护<a name="linux-machines"></a>

安全中心使用**审核**（一个最常见的 Linux 审核框架）从 linux 计算机收集审核记录。 审核位于主线内核中。 

* **Linux 审核警报和 Log Analytics 代理集成** <a name="linux-auditd"></a> -审核系统包含一个内核级别子系统，该子系统负责监视系统调用。 它按指定的规则集筛选它们，并将其消息写入套接字。 安全中心集成了 Log Analytics 代理中的审核包中的功能。 通过这种集成，可在所有受支持的 Linux 发行版中收集审核事件，无需任何先决条件。

    使用适用于 Linux 代理的 Log Analytics agent 收集、充实和聚合审核记录。 安全中心会持续添加使用 Linux 信号检测云和本地 Linux 计算机上的恶意行为的新分析。 类似于 Windows 功能，这些分析跨越可疑进程、可疑登录尝试、内核模块加载和其他活动。 这些活动可以指示计算机受到攻击或已被破坏。  

    有关 Linux 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 可以通过下载[Azure 安全中心操作手册： Linux 检测](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)来模拟 linux 警报。





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service 威胁防护<a name="app-services"></a>

> [!NOTE]
> 此服务目前在 Azure 政府版和主权云区域中不可用。

安全中心使用云的规模来确定针对应用服务上运行的应用程序的攻击。 攻击者探查 web 应用程序以找出并利用弱点。 在将路由到特定环境之前，对在 Azure 中运行的应用程序的请求会经历多个网关，并在其中进行检查和记录。 然后，将使用此数据来识别攻击者和攻击者，并了解稍后将使用的新模式。

通过使用 Azure 作为云提供商的可见性，安全中心将分析应用服务内部日志，以确定多个目标的攻击方法。 例如，方法包含广泛的扫描和分布式攻击。 这种类型的攻击通常来自一小部分 Ip，并显示对多个主机上的类似终结点进行爬网的模式。 攻击会搜索易受攻击的页面或插件，不能从单个主机的角度来识别。

如果运行的是基于 Windows 的应用服务计划，安全中心还可以访问基础沙箱和 Vm。 除了上面提到的日志数据，基础结构可以从一种新的攻击传播到客户计算机中的危害，并使其受益。 因此，即使在 web 应用被利用后部署了安全中心，它也能够检测到正在进行的攻击。

有关 Azure App Service 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureappserv)。

有关应用服务计划的详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## <a name="threat-protection-for-azure-containers"></a>Azure 容器的威胁防护<a name="azure-containers"></a>

> [!NOTE]
> 此服务目前在 Azure 政府版和主权云区域中不可用。

安全中心为容器化环境提供实时威胁保护，并生成可疑活动的警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心提供不同级别的威胁防护： 

* **主机级别**-安全中心的代理（在标准层上提供，有关详细信息，请参阅[定价](security-center-pricing.md)）监视 Linux 中的可疑活动。 对于源自节点或在其上运行的容器的可疑活动，代理会触发警报。 此类活动的示例包括 web shell 检测和与已知的可疑 IP 地址的连接。

    为了更深入地了解容器化环境的安全性，代理会监视容器特定的分析。 它将触发对在 Docker 容器中运行的事件（例如特权容器创建、可疑访问 API 服务器和安全外壳（SSH）服务器）的警报。

    >[!IMPORTANT]
    > 如果选择不在主机上安装代理，则只会收到威胁防护权益和安全警报的子集。 你仍将收到与网络分析和恶意服务器通信相关的警报。

    有关主机级别警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-containerhost)。


* 在**AKS 群集级别**，威胁防护基于分析 Kubernetes "审核日志"。 若要启用此**无代理**监视，请从 **& 设置**"页的" 定价 "中将" Kubernetes "选项添加到你的订阅（请参阅[定价](security-center-pricing.md)）。 若要在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括：公开的 Kubernetes 仪表板、创建高特权角色以及创建敏感的装载。

    >[!NOTE]
    > 安全中心为在订阅设置上启用 Kubernetes 选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

    有关 AKS 群集级别警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-akscluster)。

另外，我们的安全研究人员的全球安全研究人员不断监视威胁的发展。 它们在发现时添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>针对 SQL 数据库和 SQL 数据仓库的威胁防护<a name="data-sql"></a>

适用于 Azure SQL 数据库的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

如果出现可疑数据库活动、潜在漏洞或 SQL 注入攻击，以及异常的数据库访问和查询模式，你将看到警报。

适用于 Azure SQL 数据库和 SQL 的高级威胁防护是高级 SQL 安全功能[（](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)包括 Azure sql 数据库、Azure sql 数据库托管实例、Azure Sql 数据仓库数据库和 Azure 虚拟机上的 sql server）的高级 SQL 安全统一包的高级威胁防护。

有关详细信息，请参见:

* [如何为 Azure SQL Database 启用高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何为 Azure 虚拟机上的 SQL server 启用高级威胁防护](security-center-iaas-advanced-data.md)
* [针对 SQL 数据库和 SQL 数据仓库的威胁防护警报列表](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure 存储的威胁防护<a name="azure-storage"></a>

针对存储的高级威胁防护检测到异常和可能有害的访问或利用存储帐户的尝试。 这一层保护使你无需成为安全专家，就能解决威胁，并可帮助你管理安全监视系统。

Azure 存储的高级威胁防护目前仅适用于[Blob 存储](https://azure.microsoft.com/services/storage/blobs/)。 

此服务适用于所有公有云和美国政府云，但没有其他主权或 Azure 政府云区域。

有关定价详细信息（包括免费的30天试用版），请参阅[Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

有关详细信息，请参见:

* [如何为 Azure 存储启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 存储的威胁防护警报列表](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的说明来模拟 Azure 存储警报。




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB 威胁防护<a name="cosmos-db"></a>

Azure Cosmos DB 警报由异常和潜在有害尝试访问或利用 Azure Cosmos DB 帐户生成。

有关详细信息，请参见:

* [Azure Cosmos DB 的高级威胁防护（预览版）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威胁防护警报列表（预览）](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 网络层的威胁防护<a name="network-layer"></a>

安全中心网络层分析基于示例[IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，这些数据是由 Azure 核心路由器收集的数据包标头。 根据此数据馈送，安全中心将使用机器学习模型来识别和标记恶意流量活动。 安全中心还使用 Microsoft 威胁智能数据库来丰富 IP 地址。

某些网络配置可能会限制安全中心在可疑网络活动上生成警报。 要使安全中心生成网络警报，请确保：

- 虚拟机有一个公共 IP 地址（或在具有公共 IP 地址的负载均衡器上）。

- 外部 IDS 解决方案不阻止虚拟机的网络出口流量。

- 已为虚拟机分配的 IP 地址与发生可疑通信的整个小时相同。 这也适用于作为托管服务的一部分创建的 Vm （例如，AKS、Databricks）。

有关 Azure 网络层警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azurenetlayer)。

有关安全中心如何使用网络相关信号来应用威胁防护的详细信息，请参阅[安全中心中的启发式 DNS 检测](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理层威胁防护（Azure 资源管理器）（预览版）<a name ="management-layer"></a>

基于 Azure 资源管理器的安全中心保护层目前处于预览阶段。

安全中心通过使用 Azure 资源管理器事件（被视为 Azure 的控制平面）提供额外的保护层。 通过分析 Azure 资源管理器记录，安全中心检测到 Azure 订阅环境中的异常或潜在有害操作。

有关 Azure 资源管理器（预览版）警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 上述几个分析由 Microsoft Cloud App Security 提供支持。 若要从这些分析中获益，必须激活 Cloud App Security 许可证。 如果有 Cloud App Security 许可证，则默认情况下将启用这些警报。 若要禁用警报：
>
> 1. 在 "**安全中心**" 边栏选项卡中，选择 "**安全策略**"。 对于要更改的订阅，请选择 "**编辑设置**"。
> 2. 选择 "**威胁检测**"。
> 3. 在 "**启用集成**" 下，清除 "**允许 Microsoft Cloud App Security 访问我的数据**"，然后选择 "**保存**"。

>[!NOTE]
>安全中心将与安全相关的客户数据存储在与其资源相同的地理区域。 如果 Microsoft 尚未在资源的地理上部署安全中心，则会将数据存储在美国中。 启用 Cloud App Security 时，此信息按 Cloud App Security 的地理位置规则存储。 有关详细信息，请参阅[非区域服务的数据存储](https://azuredatacentermap.azurewebsites.net/)。








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 的威胁防护（预览版）<a name="azure-keyvault"></a>

> [!NOTE]
> 此服务目前在 Azure 政府版和主权云区域中不可用。

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

Azure 安全中心包含适用于 Azure Key Vault 的 Azure 本机高级威胁防护，提供额外的安全智能层。 安全中心检测到 Key Vault 帐户访问或利用异常的潜在有害尝试。 这一层保护使你可以在不是安全专家的情况下解决威胁，而无需管理第三方安全监视系统。  

当发生异常活动时，安全中心会显示警报，还可以选择通过电子邮件将其发送给订阅管理员。 这些警报包括可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

有关 Azure Key Vault 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azurekv)。





## <a name="threat-protection-for-other-microsoft-services"></a>其他 Microsoft 服务的威胁防护<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF 的威胁防护<a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序正逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 是基于开放式 Web 应用程序安全项目的核心规则集3.0 或2.2.9。 WAF 会自动更新，以防止出现新的漏洞。 

如果拥有 Azure WAF 的许可证，则会将 WAF 警报流式传输到安全中心，无需进行其他配置。 有关 WAF 生成的警报的详细信息，请参阅[Web 应用程序防火墙 CRS 规则组和规则](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 保护的威胁防护<a name="azure-ddos"></a>

众所周知，分布式拒绝服务（DDoS）攻击非常易于执行。 它们已成为一个很好的安全问题，尤其是在将应用程序移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可以面向任何可通过 internet 访问的终结点。

若要防御 DDoS 攻击，请购买 Azure DDoS 保护的许可证，并确保遵循应用程序设计的最佳实践。 DDoS 保护提供不同的服务层。 有关详细信息，请参阅[Azure DDoS 保护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有关 Azure DDoS 保护警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>后续步骤
若要了解有关这些威胁防护功能的安全警报的详细信息，请参阅以下文章：

* [所有 Azure 安全中心警报的引用表](alerts-reference.md)
* [Azure 安全中心的安全警报](security-center-alerts-overview.md)
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)
* [导出安全警报和建议（预览）](continuous-export.md)