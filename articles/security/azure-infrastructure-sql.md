---
title: Azure SQL 数据库安全功能
description: 本文提供有关 Azure SQL 数据库如何保护 Azure 中的客户数-据的一般说明。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cce1ff1102c42bd1627caeba7b2c86432b228607
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170848"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL 数据库安全功能    
Azure SQL 数据库在 Azure 中提供关系型数据库服务。 为了保护客户数据并提供关系型数据库服务预期具备的强大安全功能，SQL 数据库具有自身的安全功能集。 这些功能立足于从 Azure 继承的控制能力。

## <a name="security-capabilities"></a>安全功能

### <a name="usage-of-the-tds-protocol"></a>TDS 协议用法
Azure SQL 数据库仅支持表格格式数据流 (TDS) 协议，该协议要求只能通过默认端口 TCP/1433 访问数据库。

### <a name="azure-sql-database-firewall"></a>Azure SQL 数据库防火墙
为了帮助保护客户数据，Azure SQL 数据库包含防火墙功能，默认情况下，该功能会阻止对 SQL 数据库服务器的所有访问，如下所示。

![Azure SQL 数据库防火墙][1]

网关防火墙可以限制地址，使客户能够进行精细控制，以指定可接受的 IP 地址范围。 防火墙基于每个请求的来源 IP 地址授予访问权限。

客户可以使用管理门户，或者使用 Microsoft Azure SQL 数据库管理 REST API 以编程方式实现防火墙配置。 默认情况下，Azure SQL 数据库网关防火墙阻止所有客户 TDS 访问 Azure SQL 数据库实例。 客户使用访问控制列表 (ACL) 配置访问权限，允许通过源和目标 Internet 地址、协议和端口号建立 Azure SQL 数据库建立连接。

### <a name="dosguard"></a>DoSGuard
名为 DoSGuard 的 SQL 数据库网关服务可以减少拒绝服务 (DoS) 攻击。 DoSGuard 能够主动跟踪 IP 地址发起的失败登录。 如果特定的 IP 地址在一段时间内多次登录失败，则会阻止该 IP 地址在预定义的时间段内访问服务中的任何资源。

此外，Azure SQL 数据库网关还会：

- 执行安全通道功能协商，以便在连接到数据库服务器时实现 TDS FIPS 140-2 验证的加密连接。
- 在接受来自客户端的连接时执行有状态 TDS 数据包检查。 网关会验证连接信息，并根据连接字符串中指定的数据库名称，将 TDS 数据包传递给相应的物理服务器。

Azure SQL 数据库产品/服务网络安全性的首要原则是，只出于让服务正常运行的目的允许所需的连接和通信。 默认会阻止其他所有端口、协议和连接。 按源和目标网络、协议与端口号，使用虚拟局域网 (VLAN) 和 ACL 限制网络通信。

已批准用于实现基于网络的 ACL 的机制包括路由器和负载均衡器上的 ACL。 这些机制由客户配置的 Azure 网络、来宾 VM 防火墙和 Azure SQL 数据库网关防火墙规则管理。

## <a name="data-segregation-and-customer-isolation"></a>数据分离和客户隔离
Azure 生产网络的构建方式确保可公开访问的系统组件与内部资源相分离。 为面向公众的 Azure 门户提供访问权限的 Web 服务器，与客户应用程序实例和客户数据所在的底层 Azure 虚拟基础之间存在物理和逻辑边界。

所有可公开访问的信息在 Azure 生产网络中进行管理。 生产网络受双重身份验证和边界保护机制的控制，使用上一部分中所述的防火墙和安全功能集，并使用后续部分所述的数据隔离功能。

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>未经授权的系统和 FC 隔离
由于结构控制器 (FC) 是 Azure 结构的中心业务流程协调程序，因此已采取重要的控制措施来缓解它面临的威胁，尤其是来自客户应用程序中可能受到攻击的 FA 的威胁。 FC 无法识别其设备信息（例如 MAC 地址）未预先在 FC 中加载的任何硬件。 FC 上的 DHCP 服务器包含它们想要启动的节点的已配置 MAC 地址列表。 即使未经授权的系统已连接，它们也不会合并到结构库存中，因此，不会连接到结构库存中的任何系统，也无权与这些系统通信。 这降低了未经授权的系统与 FC 通信并获取 VLAN 和 Azure 的访问权限的风险。

### <a name="vlan-isolation"></a>VLAN 隔离
Azure 生产网络在逻辑上分离成三个主要 VLAN：

- 主 VLAN：互连不受信任的客户节点。
- FC VLAN：包含受信任的 FC 及支持的系统。
- 设备 VLAN：包含受信任的网络和其他基础结构设备。

### <a name="packet-filtering"></a>数据包筛选
在节点的根 OS 和来宾 OS 上实施的 IPFilter 与软件防火墙强制实施连接限制，并阻止 VM 之间未经授权的流量。

### <a name="hypervisor-root-os-and-guest-vms"></a>虚拟机监控程序、根 OS 和来宾 VM
根 OS 与来宾 VM 之间的隔离以及不同来宾 VM 之间的隔离，由虚拟机监控程序和根 OS 管理。

### <a name="types-of-rules-on-firewalls"></a>防火墙上的规则类型
规则定义为：

{安全响应中心 (Src) IP，Src 端口，目标 IP，目标端口，目标协议，传入/传出，有状态/无状态，有状态流超时}。

仅当受任一规则的允许时，才允许传入或传出同步空闲字符 (SYN) 数据包。 对于 TCP，Azure 使用无状态规则，其中的原则是，只允许所有非 SYN 数据包传入或传出 VM。 安全性的前提是，如果任何主机堆栈以前未发现 SYN 数据包，则灵活忽略非 SYN 数据包。 TCP 协议本身是有状态的，与无状态的基于 SYN 的规则相结合，实现有状态实施方案的整体行为。

对于用户数据报协议 (UDP)，Azure 使用有状态规则。 每当 UDP 数据包与规则匹配时，就会朝另一个方向创建反向流。 此流具有内置的超时。

客户需负责在 Azure 提供的功能的基础上设置自己的防火墙。 此处，客户可以针对入站和出站流量定义规则。

### <a name="production-configuration-management"></a>生产配置管理
标准的安全配置由相应的运营团队在 Azure 和 Azure SQL 数据库中维护。 通过中心跟踪系统阐述和跟踪对生产系统做出的所有配置更改。 通过中心跟踪系统跟踪软件和硬件更改。 使用 ACL 管理服务跟踪与 ACL 相关的网络更改。

对 Azure 做出的所有配置在过渡环境中进行开发和测试，然后部署在生产环境中。 软件内部版本在测试过程中进行评审。 安全和隐私检查作为入口清单条件的一部分进行评审。 相应的部署团队根据计划的时间间隔部署更改。 在将发行版部署到生产环境之前，相应的部署团队人员会对其进行评审和签收。

将会监视更改是否成功。 发生故障时，会将更改回滚到其以前的状态，或者在得到指定人员的批准的情况下，部署修补程序来解决故障。 使用 Source Depot、Git、TFS、Master Data Services (MDS)、Runners、Azure 安全监视、FC 和 WinFabric 平台在 Azure 虚拟环境中集中管理、应用和验证配置设置。

同样，将对硬件和网络更改运行已建立的验证步骤来评估它们是否符合生成要求。 通过相关小组的协调变更咨询委员会 (CAB) 在整个堆栈上评审和授权发行版。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [Azure 基础结构监视](azure-infrastructure-monitoring.md)
- [Azure 基础结构完整性](azure-infrastructure-integrity.md)
- [Azure 客户数据保护](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
