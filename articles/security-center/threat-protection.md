---
title: Azure 安全中心中的威胁保护
description: 本主题介绍受 Azure 安全中心威胁保护功能保护的资源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 395f4b5481fcf2028d6bfe736e58c3174a0c80b2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435349"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 安全中心中的威胁保护

当安全中心检测到环境的任何区域中的威胁时，它会生成警报。 这些警报描述受影响资源的详细信息、建议的补救步骤，在某些情况下还描述了在响应中触发逻辑应用的选项。

Azure 安全中心的威胁防护为您的环境提供了全面的防御：

* **Azure 计算资源的威胁保护**：Windows 计算机、Linux 计算机、Azure 应用服务和 Azure 容器

* **Azure 数据资源的威胁保护**：SQL 数据库和 SQL 数据仓库、Azure 存储和 Azure 宇宙数据库

* **Azure 服务层的威胁保护**：Azure 网络层、Azure 管理层（Azure 资源管理器）（预览）和 Azure 密钥保管库（预览）

无论是安全中心生成警报，还是安全中心从其他安全产品接收警报，您都可以导出警报。 要将警报导出到 Azure Sentinel（或第三方 SIEM）或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md)中的说明进行操作。 




## <a name="threat-protection-for-windows-machines"></a>Windows 计算机的威胁保护<a name="windows-machines"></a>

Azure 安全中心与 Azure 服务集成，以监视和保护基于 Windows 的计算机。 安全中心以易于使用的格式提供所有这些服务的警报和补救建议。

* **微软防御者ATP** <a name="windows-atp"></a> - 安全中心通过与微软防御者高级威胁保护 （ATP） 集成来扩展其云工作负载保护平台。 它们共同提供全面的端点检测和响应 （EDR） 功能。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 服务器上会自动启用 Microsoft Defender ATP 传感器。

    当微软防御者 ATP 检测到威胁时，它会触发警报。 警报显示在安全中心仪表板上。 从仪表板，您可以透视到 Microsoft Defender ATP 控制台，并执行详细调查以发现攻击的范围。 有关微软后卫 ATP 的更多信息，请参阅[微软防御者 ATP 服务的板载服务器](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **崩溃转储分析**<a name="windows-dump"></a>- 当软件崩溃时，崩溃转储在崩溃时捕获部分内存。

    崩溃可能是由恶意软件引起的或包含恶意软件。 为了避免被安全产品检测到，各种形式的恶意软件使用无文件攻击，避免写入磁盘或加密写入磁盘的软件组件。 使用传统的基于磁盘的方法很难检测到这种类型的攻击。

    但是，通过使用内存分析，您可以检测到这种攻击。 通过分析崩溃转储中的内存，安全中心可以检测攻击正在使用的技术。 例如，攻击可能试图利用软件中的漏洞，访问机密数据，并秘密地在受攻击的机器中持续存在。 安全中心以对主机的性能影响最小来完成此工作。

    有关崩溃转储分析警报的详细信息，请参阅[警报的参考表](alerts-reference.md#alerts-windows)。

* **无文件攻击检测**<a name="windows-fileless"></a>- 针对端点的无文件攻击很常见。 为了避免检测，无文件攻击将恶意负载注入内存。 攻击者负载保留在受破坏进程的内存中，并执行各种恶意活动。

    通过无文件攻击检测，自动内存取证技术可识别无文件攻击工具包、技术和行为。 此解决方案会在运行时定期扫描计算机，并直接从安全关键型进程的内存中提取见解。

    它找到恶意负载的利用、代码注入和执行的证据。 无文件攻击检测生成详细的安全警报，以加快警报会审、关联和下游响应时间。 此方法补充了基于事件的 EDR 解决方案，提供了更大的检测覆盖率。

    有关无文件攻击检测警报的详细信息，请参阅[警报的参考表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 您可以通过下载[Azure 安全中心行动手册：安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)来模拟 Windows 警报。






## <a name="threat-protection-for-linux-machines"></a>Linux 计算机的威胁保护<a name="linux-machines"></a>

安全中心使用最常见的Linux审核框架之一的**审核**框架，从Linux计算机收集审计记录。 审核生活在主线内核中。 

* **Linux 审核警报和日志分析代理集成**<a name="linux-auditd"></a>- 审核的系统由一个内核级子系统组成，负责监视系统调用。 它按指定的规则集筛选它们，并将它们的消息写入套接字。 安全中心将审核包的功能集成到日志分析代理中。 此集成支持在所有支持的 Linux 发行版中收集审核的事件，而无需任何先决条件。

    使用 Linux 代理的日志分析代理收集、丰富和聚合到事件中审核的记录。 安全中心不断添加新的分析，使用 Linux 信号检测云和本地 Linux 计算机上的恶意行为。 与 Windows 功能类似，这些分析跨越可疑进程、可疑的登录尝试、内核模块加载和其他活动。 这些活动可能表明机器受到攻击或已被入侵。  

    有关 Linux 警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 您可以通过下载[Azure 安全中心行动手册：Linux 检测](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)来模拟 Linux 警报。





## <a name="threat-protection-for-azure-app-service"></a>Azure 应用服务的威胁保护<a name="app-services"></a>

> [!NOTE]
> 此服务当前不在 Azure 政府和主权云区域中。

安全中心使用云的规模来识别针对通过应用服务运行的应用程序的攻击。 攻击者将调查 Web 应用程序以查找和利用弱点。 在路由到特定环境之前，对 Azure 中运行的应用程序的请求将经过多个网关，其中将对其进行检查和记录。 然后，此数据用于识别漏洞利用和攻击者，并了解稍后将使用的新模式。

通过使用 Azure 作为云提供商的可见性，安全中心分析应用服务内部日志，以确定对多个目标的攻击方法。 例如，方法包括广泛的扫描和分布式攻击。 这种类型的攻击通常来自 IP 的一小部分，并显示爬网到多个主机上的类似终结点的模式。 攻击正在搜索易受攻击的页面或插件，无法从单个主机的角度进行识别。

如果您正在运行基于 Windows 的应用服务计划，安全中心还可以访问基础沙盒和 VM。 与上述日志数据一起，基础结构可以讲述故事，从在野外循环的新攻击到客户机器中的危害。 因此，即使安全中心是在 Web 应用被利用后部署的，它也可能检测到正在进行的攻击。

有关 Azure 应用服务警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-azureappserv)。

有关应用服务计划的详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## <a name="threat-protection-for-azure-containers"></a>Azure 容器的威胁保护<a name="azure-containers"></a>

> [!NOTE]
> 此服务当前不在 Azure 政府和主权云区域中。

安全中心为您的容器化环境提供实时威胁保护，并针对可疑活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同级别提供威胁保护： 

* **主机级**- 安全中心的代理（在标准层上提供，请参阅[定价](security-center-pricing.md)的详细信息）监视 Linux 中的可疑活动。 代理会触发来自节点或运行在节点上的容器的可疑活动的警报。 此类活动的示例包括 Web 外壳检测和与已知可疑 IP 地址的连接。

    为了更深入地了解容器化环境的安全性，代理监视特定于容器的分析。 它将触发对事件警报，例如特权容器创建、对 API 服务器的可疑访问以及 Docker 容器内运行的安全外壳 （SSH） 服务器。

    >[!IMPORTANT]
    > 如果选择不在主机上安装代理，则只会收到威胁保护权益和安全警报的子集。 您仍将收到与网络分析和与恶意服务器通信相关的警报。

    有关主机级警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-containerhost)。


* 在**AKS集群级别**，威胁保护基于分析库伯内斯的审计日志。 要启用此**无代理**监视，请从**定价&设置**页将 Kubernetes 选项添加到订阅中（请参阅[定价](security-center-pricing.md)）。 若要在此级别生成警报，安全中心使用 AKS 检索的日志监视 AKS 托管的服务。 此级别的事件示例包括公开的 Kubernetes 仪表板、创建高特权角色以及创建敏感装载。

    >[!NOTE]
    > 安全中心为在订阅设置上启用 Kubernetes 选项后发生的 Azure 库伯内特服务操作和部署生成安全警报。 

    有关 AKS 群集级别警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-akscluster)。

此外，我们的全球安全研究人员团队不断监控威胁形势。 在发现容器时，它们会添加特定于容器的警报和漏洞。

> [!TIP]
> 您可以按照[本博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL 数据库和 SQL 数据仓库的威胁保护<a name="data-sql"></a>

Azure SQL 数据库的高级威胁保护可检测异常活动，指示访问或利用数据库的异常和潜在有害尝试。

当存在可疑的数据库活动、潜在漏洞或 SQL 注入攻击以及异常的数据库访问和查询模式时，您将看到警报。

Azure SQL 数据库和 SQL 的高级威胁防护是[高级 SQL 安全功能的高级数据安全 （ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)统一包的一部分，涵盖 Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 数据仓库数据库和 Azure 虚拟机上的 SQL 服务器。

有关详细信息，请参阅：

* [如何为 Azure SQL 数据库启用高级威胁保护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何为 Azure 虚拟机上的 SQL 服务器启用高级威胁保护](security-center-iaas-advanced-data.md)
* [SQL 数据库和 SQL 数据仓库的威胁防护警报列表](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Azure 存储的威胁保护<a name="azure-storage"></a>

> [!NOTE]
> 此服务在美国政府云中可用，但没有其他主权或 Azure 政府云区域。

高级存储威胁保护（目前仅适用于 Blob 存储）可检测访问或利用存储帐户的异常和潜在有害尝试。 此保护层允许您解决威胁，而无需您成为安全专家，并帮助您管理安全监控系统。

有关详细信息，请参阅：

* [如何为 Azure 存储启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 存储的威胁保护警报列表](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> 您可以按照[本博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的说明来模拟 Azure 存储警报。




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure 宇宙 DB 的威胁保护<a name="cosmos-db"></a>

Azure 宇宙数据库警报由访问或利用 Azure Cosmos DB 帐户的异常且可能有害的尝试生成。

有关详细信息，请参阅：

* [Azure 宇宙 DB 的高级威胁保护（预览版）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure 宇宙 DB 的威胁保护警报列表（预览版）](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 网络层的威胁保护<a name="network-layer"></a>

安全中心网络层分析基于[示例 IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，这些数据是由 Azure 核心路由器收集的数据包标头。 基于此数据馈送，安全中心使用机器学习模型来识别和标记恶意流量活动。 安全中心还使用 Microsoft 威胁情报数据库来丰富 IP 地址。

某些网络配置可能会限制安全中心生成可疑网络活动的警报。 对于安全中心生成网络警报，请确保：

- 您的虚拟机具有公共 IP 地址（或位于具有公共 IP 地址的负载均衡器上）。

- 外部 IDS 解决方案不会阻止虚拟机的网络出口流量。

- 在发生可疑通信的整个小时内，虚拟机都分配了相同的 IP 地址。 这也适用于作为托管服务的一部分创建的 VM（例如 AKS、数据砖）。

有关 Azure 网络图层警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-azurenetlayer)。

有关安全中心如何使用网络相关信号应用威胁保护的详细信息，请参阅[安全中心中的启发式 DNS 检测](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理层（Azure 资源管理器）的威胁保护（预览）<a name ="management-layer"></a>

基于 Azure 资源管理器的安全中心保护层当前处于预览状态。

安全中心通过使用 Azure 资源管理器事件提供额外的保护层，该事件被视为 Azure 的控制平面。 通过分析 Azure 资源管理器记录，安全中心检测 Azure 订阅环境中的异常或潜在有害操作。

有关 Azure 资源管理器（预览）警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 前面的几个分析由 Microsoft 云应用安全支持。 要从这些分析中获益，您必须激活云应用安全许可证。 如果您有云应用安全许可证，则默认情况下将启用这些警报。 要禁用警报：
>
> 1. 在 **"安全中心**"边栏选项卡中，选择**安全策略**。 对于要更改的订阅，请选择 **"编辑设置**"。
> 2. 选择**威胁检测**。
> 3. 在**启用集成**下，清除**允许 Microsoft 云应用安全访问我的数据**，然后选择"**保存**"。

>[!NOTE]
>安全中心将安全相关的客户数据存储在与其资源相同的地理位置中。 如果 Microsoft 尚未在资源的地理部署安全中心，则它将数据存储在美国。 启用云应用安全性后，此信息将按照云应用安全性的地理位置规则进行存储。 有关详细信息，请参阅[非区域服务的数据存储](https://azuredatacentermap.azurewebsites.net/)。








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 密钥保管库的威胁保护（预览版）<a name="azure-keyvault"></a>

> [!NOTE]
> 此服务当前不在 Azure 政府和主权云区域中。

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

Azure 安全中心包括 Azure 密钥保管库的 Azure 本机高级威胁保护，提供了额外的安全智能层。 安全中心检测访问或利用密钥保管库帐户的异常且可能有害的尝试。 此保护层允许您无需成为安全专家即可应对威胁，而无需管理第三方安全监控系统。  

当异常活动发生时，安全中心会显示警报，并选择性地通过电子邮件将其发送给订阅管理员。 这些警报包括可疑活动的详细信息以及如何调查和补救威胁的建议。 

有关 Azure 密钥保管库警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-azurekv)。





## <a name="threat-protection-for-other-microsoft-services"></a>其他 Microsoft 服务的威胁保护<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF 的威胁保护<a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 基于打开 Web 应用程序安全项目的核心规则集 3.0 或 2.2.9。 WAF 会自动更新，以防止新的漏洞。 

如果您有 Azure WAF 的许可证，则 WAF 警报将流式传输到安全中心，无需其他配置。 有关 WAF 生成的警报的详细信息，请参阅 Web[应用程序防火墙 CRS 规则组和规则](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 保护的威胁保护<a name="azure-ddos"></a>

众所周知，分布式拒绝服务 （DDoS） 攻击很容易执行。 它们已成为一个很大的安全问题，尤其是在您将应用程序迁移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可以针对任何可以通过 Internet 到达的终结点。

要抵御 DDoS 攻击，请购买 Azure DDoS 保护许可证，并确保遵循应用程序设计最佳实践。 DDoS 保护提供不同的服务层。 有关详细信息，请参阅[Azure DDoS 保护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有关 Azure DDoS 保护警报的列表，请参阅[警报的参考表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>后续步骤
要了解有关这些威胁防护功能的安全警报的更多详细信息，请参阅以下文章：

* [所有 Azure 安全中心警报的参考表](alerts-reference.md)
* [Azure 安全中心的安全警报](security-center-alerts-overview.md)
* [管理和响应 Azure 安全中心中的安全警报](security-center-managing-and-responding-alerts.md)
* [导出安全警报和建议（预览版）](continuous-export.md)