---
title: Azure 安全中心的威胁防护
description: 本主题介绍受 Azure 安全中心威胁防护功能保护的资源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e2f72cc0ea6851caaf5d0db2f17f8e16473d420e
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667552"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 安全中心的威胁防护

当安全中心检测到环境中的任何区域遭到威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。

Azure 安全中心威胁防护为环境提供全面的防御：

* **针对 Azure 计算资源的威胁防护**：Windows 计算机、Linux 计算机、Azure 应用服务和 Azure 容器

* **Azure 数据资源的威胁防护**： sql 数据库和 Azure Synapse Analytics (以前的 Sql 数据仓库) 、Azure 存储和 Azure Cosmos DB

* **针对 Azure 服务层的威胁防护**：Azure 网络层、Azure 管理层（Azure 资源管理器）（预览版）和 Azure Key Vault（预览版）

无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。 

> [!NOTE]
> 来自不同源的警报可能在不同的时间后出现。 例如，需要分析网络流量的警报的出现时间，可能比虚拟机上运行的可疑进程的相关警报要晚一些。

> [!TIP]
> 若要启用安全中心的威胁防护功能，必须将标准定价层应用到包含适用工作负荷的订阅。
>
> 可以在订阅级别或资源级别为 **Azure 存储帐户**启用威胁防护。
> 可以在订阅级别或资源级别为 **Azure SQL 数据库 SQL 服务器**启用威胁防护。
> 只能在资源级别为 **Azure Database for MariaDB/MySQL/PostgreSQL** 启用威胁防护。



## <a name="threat-protection-for-windows-machines"></a>针对 Windows 计算机的威胁防护 <a name="windows-machines"></a>

Azure 安全中心与 Azure 服务集成，可以监视和保护基于 Windows 的计算机。 安全中心以易用的格式提供来自所有这些服务的警报和修正建议。

* **Microsoft Defender 高级威胁防护 (ATP)** <a name="windows-atp"></a> - 安全中心通过与 Microsoft Defender 高级威胁防护 (ATP) 相集成扩展了其云工作负荷保护平台。 两者共同提供全面的终结点检测和响应 (EDR) 功能。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 服务器上已自动启用 Microsoft Defender ATP 传感器。

    Microsoft Defender ATP 在检测到威胁时会触发警报。 警报显示在安全中心仪表板上。 在仪表板中，可以透视 Microsoft Defender ATP 控制台，并执行详细调查来发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅[将服务器加入 Microsoft Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **Fileless 攻击检测** <a name="windows-fileless"></a> -Fileless 攻击会将恶意有效负载注入到内存中，以避免通过基于磁盘的扫描技术检测。 然后，攻击者的负载会在遭到攻击的进程中保持不变，并执行各种恶意活动。

    自动内存取证技术使用无文件攻击检测来识别无文件攻击工具包、方法和行为。 此解决方案会定期在运行时扫描计算机，并直接从进程内存中提取见解。 适用于 Linux 的特定见解包括以下内容的标识： 

    - 众所周知的工具包和加密挖掘软件 
    - 外壳代码是一小段代码，通常用作利用软件漏洞的有效负载。
    - 进程内存中注入的恶意可执行文件

    Fileless 攻击检测会生成详细的安全警报，其中包含具有其他进程元数据的说明，例如网络活动。 这加快了警报会审、相关和下游响应时间。 此方法补充基于事件的 EDR 解决方案，并提供更高的检测范围。

    有关无文件攻击检测警报的详细信息，请参阅[警报参考表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 可以下载 [Azure 安全中心 Playbook：安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)来模拟 Windows 警报。






## <a name="threat-protection-for-linux-machines"></a>针对 Linux 计算机的威胁防护 <a name="linux-machines"></a>

安全中心使用 **auditd**（最常见的 Linux 审核框架之一）从 Linux 计算机收集审核记录。 auditd 驻留在主线内核中。 

* **Linux auditd 警报和 Log Analytics 代理集成** <a name="linux-auditd"></a> - auditd 系统包含一个负责监视系统调用的内核级子系统。 该子系统会按照指定的规则集筛选这些调用，并将针对这些调用生成的消息写入到套接字。 安全中心在 Log Analytics 代理中集成了 auditd 包的功能。 通过这种集成，无需满足任何先决条件，就能在所有受支持的 Linux 发行版中收集 auditd 事件。

    可以使用适用于 Linux 的 Log Analytics 代理收集、扩充 auditd 记录并将其聚合到事件中。 安全中心会持续添加新分析功能，这些功能可以使用 Linux 信号来检测云和本地 Linux 计算机上的恶意行为。 类似于 Windows 中的功能，这些分析功能可以检测各种可疑进程、可疑登录企图、内核模块加载操作和其他活动。 这些活动可能表示计算机正在受到攻击或已遭入侵。  

    有关 Linux 警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 可以下载 [Azure 安全中心 Playbook：Linux 检测](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)来模拟 Linux 警报。





## <a name="threat-protection-for-azure-app-service"></a>针对 Azure 应用服务的威胁防护 <a name="app-services"></a>

> [!NOTE]
> 此服务目前在 Azure 政府云和主权云区域中不可用。

安全中心使用云的规模来识别针对应用服务运行的应用程序受到的攻击。 攻击者会探查 Web 应用程序，以找出并恶意利用其中的弱点。 向 Azure 中运行的应用程序发出的请求在路由到特定的环境之前会流经多个网关，网关会对其进行检查并记录其状态。 然后，将使用这些数据来识别恶意利用行为和攻击者，并了解稍后要使用的新模式。

凭借云提供商 Azure 提供的可见性，安全中心可以分析应用服务的内部日志，以识别多个目标上的攻击方法。 例如，方法包括大范围扫描和分布式攻击。 此类攻击通常来自一小部分 IP，会展示多个主机上类似终结点的爬网模式。 攻击会搜索有漏洞的页面或插件，从单个主机的角度无法识别到。

如果运行基于 Windows 的应用服务计划，则安全中心还可以访问底层沙盒与 VM。 结合上面所述的日志数据，基础结构可以讲述从四处传播的新攻击，到客户计算机遭到入侵的整个历程。 因此，即使是在 Web 应用被恶意利用后再部署安全中心，它也能够检测不断发生的攻击。

有关 Azure 应用服务警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureappserv)。

有关应用服务计划的详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## <a name="threat-protection-for-containers"></a>容器的威胁防护 <a name="azure-containers"></a>

### <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式版|
|计价|标准层|
|必需的角色和权限：|**安全管理员** 可以解除警报。<br>**安全读取者**可以查看结果。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-azure-synapse-analytics-formerly-sql-data-warehouse"></a> (以前的 SQL 数据仓库的 SQL 数据库和 Azure Synapse Analytics 的威胁防护) <a name="data-sql"></a>

Azure SQL 数据库的高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试。

出现可疑的数据库活动、潜在漏洞，或者 SQL 注入攻击以及异常的数据库访问和查询模式时，你会看到警报。

适用于 Azure SQL 数据库和 SQL 的高级威胁防护是 [高级的数据安全 (ADS) ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 统一包，其中包括 Azure sql 数据库、Azure Sql 托管实例、Azure Synapse Analytics (以前的 Sql 数据仓库) 数据库和 Azure 虚拟机上的 sql server。

有关详细信息，请参阅：

* [如何为 Azure SQL 数据库启用高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何为 Azure 虚拟机上的 SQL 服务器启用高级威胁防护](security-center-iaas-advanced-data.md)
* [ (以前的 SQL 数据仓库的 SQL 数据库和 Azure Synapse Analytics 的威胁防护警报列表) ](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>针对 Azure 存储的威胁防护 <a name="azure-storage"></a>

### <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|[Blob 存储](https://azure.microsoft.com/services/storage/blobs/) (公开上市) <br>[Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (预览) <br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (预览版) |
|计价|标准层|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||


### <a name="whats-protected"></a>受保护的内容

Azure 存储的威胁防护会在 Azure 存储帐户上检测潜在的有害活动。 无论数据存储为 blob 容器、文件共享还是数据 lake，都可以对其进行保护。

这一层保护使你 *无* 需成为安全专家，就能解决威胁，并可帮助你管理安全监视系统。

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>哪些类型的警报对 Azure 存储提供威胁保护？

当存在以下情况时，会触发安全警报：

- **可疑活动** -例如，存储帐户已从称为 Tor 的活动退出节点的 IP 地址成功访问
- **异常行为** -例如，对存储帐户的访问模式的更改
- 已**上传潜在恶意软件**-哈希信誉分析表明上传的文件包含恶意软件

警报包含触发这些事件的事件的详细信息，并提供有关如何调查和修正威胁的建议。

### <a name="what-is-hash-reputation-analysis-for-malware"></a>什么是恶意软件的哈希信誉分析？

若要确定上传的文件是否可疑，Azure 存储的威胁防护使用 [Microsoft 威胁情报](https://go.microsoft.com/fwlink/?linkid=2128684)支持的哈希信誉分析。 威胁防护工具不会扫描已上传的文件，而是检查存储日志，并将新上载文件的哈希与已知病毒、特洛伊木马程序、间谍软件和勒索软件的哈希进行比较。 

当怀疑某个文件包含恶意软件时，安全中心将显示一个警报，还可以选择通过电子邮件发送存储所有者以批准删除可疑文件。 若要设置自动删除哈希信誉分析指出包含恶意软件的文件，请部署 [工作流自动化，使其在包含 "可能已上传到存储帐户的恶意软件" 的警报上触发](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。



### <a name="next-steps"></a>后续步骤 

有关定价详细信息（包括 30 天免费试用版），请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

有关详细信息，请参阅：

* [如何为 Azure 存储启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [针对 Azure 存储的威胁防护警报列表](alerts-reference.md#alerts-azurestorage)
* [Microsoft 的威胁情报功能](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> 可以按照 [此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的说明来模拟存储警报。







## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB (预览版的威胁防护) <a name="cosmos-db"></a>

当有人企图以非寻常和可能有害的方式访问或恶意利用 Azure Cosmos DB 帐户时，会生成 Azure Cosmos DB 警报。

有关详细信息，请参阅：

* [针对 Azure Cosmos DB 的高级威胁防护（预览版）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [针对 Azure Cosmos DB 的威胁防护警报列表（预览版）](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>针对 Azure 网络层的威胁防护 <a name="network-layer"></a>

安全中心网络层分析基于示例 [IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)（Azure 核心路由器收集的数据包头）。 根据此数据馈送，安全中心使用机器学习模型来识别和标记恶意流量活动。 安全中心还使用 Microsoft 威胁情报数据库来扩充 IP 地址。

某些网络配置可能会限制安全中心对可疑网络活动生成警报。 要使安全中心生成网络警报，请确保：

- 虚拟机有一个公共 IP 地址（或位于使用公共 IP 地址的负载均衡器上）。

- 虚拟机的网络出口流量未被外部 IDS 解决方案阻止。

- 在发生可疑通信的整小时内，为虚拟机分配了同一个 IP 地址。 这也适用于作为托管服务（例如 AKS、Databricks）的一部分创建的 VM。

有关 Azure 网络层警报的列表，请参阅[警报引用表](alerts-reference.md#alerts-azurenetlayer)。




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理层（Azure 资源管理器）的威胁防护（预览）<a name ="management-layer"></a>

基于 Azure 资源管理器的安全中心保护层目前为预览版。

安全中心通过 Azure 资源管理器事件提供额外的保护层，该保护层被视为 Azure 的控制平面。 通过分析 Azure 资源管理器记录，安全中心可以检测 Azure 订阅环境中的非寻常或可能有害的操作。

有关 Azure 资源管理器（预览版）警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 上述几项分析由 Microsoft Cloud App Security 提供支持。 若要从这些分析中获益，必须激活 Cloud App Security 许可证。 如果你有 Cloud App Security 许可证，则默认会启用这些警报。 若要禁用警报：
>
> 1. 从安全中心的菜单中，选择 " **定价 & 设置**"。
> 1. 选择想要更改的订阅。
> 1. 选择“威胁检测”。
> 1. 清除 " **允许 Microsoft Cloud App Security 访问我的数据**"，然后选择 " **保存**"。

>[!NOTE]
>安全中心将安全相关的客户数据存储在其资源所在的地理区域。 如果 Microsoft 尚未在该资源所在的地理位置部署安全中心，则数据将存储在美国。 启用 Cloud App Security 后，将根据 Cloud App Security 的地理位置规则存储此信息。 有关详细信息，请参阅[非区域性服务的数据存储](https://azuredatacentermap.azurewebsites.net/)。

1. 设置要在其上安装代理的工作区。 确保该工作区位于安全中心内所用的同一个订阅中，并且你对该工作区拥有读/写权限。

1. 设置标准定价层，然后选择 " **保存**"。






## <a name="threat-protection-for-azure-key-vault-preview"></a>针对 Azure Key Vault（预览版）的威胁防护<a name="azure-keyvault"></a>

> [!NOTE]
> 此服务目前在 Azure 政府云和主权云区域中不可用。

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

Azure 安全中心包含针对 Azure Key Vault 的 Azure 原生高级威胁防护，提供额外的安全情报层。 安全中心会检测以非寻常和可能有害方式访问或恶意利用 Key Vault 帐户的企图。 借助此保护层，无需成为安全专家，也无需管理第三方安全监视系统，即可应对威胁。  

当发生异常活动时，安全中心会显示警报，并选择性地通过电子邮件将警报发送给订阅管理员。 这些警报包含可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

有关 Azure Key Vault 警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azurekv)。





## <a name="threat-protection-for-other-microsoft-services"></a>针对其他 Microsoft 服务的威胁防护 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>针对 Azure WAF 的威胁防护 <a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 基于开放 Web 应用程序安全项目中的核心规则集 3.0 或 2.2.9。 WAF 会自动更新，以便在出现新漏洞后提供保护。 

如果你有 Azure WAF 许可证，则无需进行额外的配置，就会将 WAF 警报流式传输到安全中心。 有关 WAF 生成的警报的详细信息，请参阅 [Web 应用程序防火墙 CRS 规则组和规则](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>针对 Azure DDoS 防护的威胁防护 <a name="azure-ddos"></a>

众所周知，分布式拒绝服务 (DDoS) 攻击是很容易执行的。 它们已成为极大的安全隐患，尤其是要将应用程序转移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击能够以任何可通过 Internet 访问的终结点作为目标。

若要抵御 DDoS 攻击，请购买 Azure DDoS 防护许可证，并确保遵循有关应用程序设计的最佳做法。 DDoS 防护提供不同的服务层级。 有关详细信息，请参阅 [Azure DDoS 防护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有关 Azure DDoS 防护警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>后续步骤
若要详细了解这些威胁防护功能生成的安全警报，请参阅以下文章：

* [所有 Azure 安全中心警报的参考表](alerts-reference.md)
* [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
* [在 Azure 安全中心内管理和响应安全警报](security-center-managing-and-responding-alerts.md)
* [导出安全警报和建议（预览版）](continuous-export.md)