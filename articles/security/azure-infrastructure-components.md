---
title: Azure 信息系统的组件和边界
description: 本文提供有关 Microsoft Azure 体系结构和管理的一般说明。
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
ms.openlocfilehash: b2e8ef232e1b25c7d000f4683830ff2e188047fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186470"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure 信息系统的组件和边界
本文提供有关 Azure 体系结构和管理的一般说明。 Azure 系统环境由以下网络组成：

- Microsoft Azure 生产网络（Azure 网络）
- Microsoft 企业网络 (corpnet)

独立的 IT 团队负责操作和维护这些网络。

## <a name="azure-architecture"></a>Azure 体系结构
Azure 是一个云计算平台和基础结构，用于通过数据中心的网络构建、部署和管理应用程序与服务。 Microsoft 管理这些数据中心。 基于指定的资源数量，Azure 会根据资源需求创建虚拟机 (VM)。 这些 VM 在 Azure 虚拟机监控程序中运行，该程序只能在云中使用，而不会提供给公众访问。

在每个 Azure 物理服务器节点上，有一个虚拟机监控程序直接通过硬件运行。 虚拟机监控程序将节点划分为数量可变的来宾 VM。 每个节点还提供一个根 VM，用于运行主机操作系统。 每个 VM 上已启用 Windows 防火墙。 通过配置服务定义文件来定义可寻址的端口。 只有这些端口是开放的，且可在内部或外部寻址的端口。 发往磁盘和网络的所有流量以及对其的访问均由虚拟机监控程序和根操作系统进行调解。

在主机层，Azure VM 运行最新 Windows Server 的定制强化版本。 Azure 使用的 Windows Server 版本只包含托管 VM 所需的组件。 这可以提高性能，并减小受攻击面。 机器边界由虚拟机监控程序实施，不依赖于操作系统安全性。

### <a name="azure-management-by-fabric-controllers"></a>通过结构控制器进行 Azure 管理

在 Azure 中，物理服务器（刀片服务器/节点）上运行的 VM 分组为大约由 1000 个 VM 构成的群集。 这些 VM 由一个横向扩展的冗余平台软件组件（称为结构控制器 (FC)）单独管理。

每个 FC 管理其群集中运行的应用程序的生命周期，预配并监视受其控制的硬件的运行状况。 它会运行自主操作，例如，在确定服务器出现故障时，它会在正常的服务器上重建 VM 实例。 FC 还会执行应用程序管理操作，例如部署、更新和横向扩展应用程序。

数据中心划分为群集。 群集在 FC 级别隔离故障，并防止特定种类的错误影响到发生这些错误的群集以外的服务器。 为特定 Azure 群集提供服务的 FC 分组到 FC 群集。

### <a name="hardware-inventory"></a>硬件库存

FC 在启动配置过程中准备 Azure 硬件和网络设备的库存。 进入 Azure 生产环境的任何新硬件和网络组件必须遵循启动配置过程。 FC 负责管理 datacenter.xml 配置文件中列出的整个库存。

### <a name="fc-managed-operating-system-images"></a>FC 托管的操作系统映像

操作系统团队以虚拟硬盘的形式提供映像，这些映像将部署到 Azure 生产环境中的所有主机和来宾 VM。 该团队通过自动化的脱机生成过程构建这些基本映像。 基本映像是操作系统的一个版本，其中的内核和其他核心组件已经过修改和优化，可支持 Azure 环境。

有三种类型的结构托管操作系统映像：

- 主机：在主机 VM 上运行的定制操作系统。
- 本机：在租户（例如 Azure 存储）上运行的本机操作系统。 此操作系统不包含任何虚拟机监控程序。
- 来宾：在来宾 VM 上运行的来宾操作系统。

主机和本机 FC 托管的操作系统只能在云中使用，不可供公众访问。

#### <a name="host-and-native-operating-systems"></a>主机和本机操作系统

主机和本机操作系统是强化的操作系统映像，它们托管结构代理，并在计算节点（作为节点上的第一个 VM 运行）和存储节点上运行。 使用主机和本机操作系统的优化基本映像的好处在于，可以减少 API 或未使用的组件公开的外围应用。 这些 API 或组件可能存在较高的安全风险，并增大操作系统的覆盖范围。 覆盖范围减小的操作系统只包括 Azure 所需的组件。

#### <a name="guest-operating-system"></a>来宾操作系统

来宾操作系统 VM 上运行的 Azure 内部组件无法运行远程桌面协议。 对基线配置设置所做的任何更改都必须经历更改和发布管理过程。

## <a name="azure-datacenters"></a>Azure 数据中心
Microsoft 云基础结构和运营 (MCIO) 团队管理所有 Microsoft 联机服务的物理基础结构和数据中心设施。 MCIO 主要负责管理数据中心内的物理和环境控制，以及管理和支持外围网络设备（例如边缘路由器和数据中心路由器）。 MCIO 还负责在数据中心内的机架上设置最基本的服务器硬件。 客户无法直接与 Azure 交互。

## <a name="service-management-and-service-teams"></a>服务管理和服务团队
Azure 服务的支持由称作“服务团队”的多个工程小组来管理。 每个服务团队负责某个方面的 Azure 支持工作。 每个服务团队必须指派一名全天候工作的工程师，以调查和解决服务中的故障。 默认情况下，服务团队无法对 Azure 中运行的硬件进行实物接触。

服务团队负责：

- 应用程序平台
- Azure Active Directory
- Azure 计算
- Azure Net
- 云工程服务
- ISSD：安全性
- 多重身份验证
- SQL 数据库
- 存储

## <a name="types-of-users"></a>用户类型
Microsoft 的员工（或合同工）被视为内部用户。 其他所有用户被视为外部用户。 所有 Azure 内部用户都有一种根据敏感级别分类的员工状态，该状态定义了相应用户对客户数据的访问权限（有访问权限或无访问权限）。 下表描述了用户对 Azure 的特权（身份验证后的授权权限）：

| 角色 | 内部或外部 | 敏感级别 | 履行的授权特权和功能 | 访问类型
| --- | --- | --- | --- | --- |
| Azure 数据中心工程师 | 内部 | 无权访问客户数据 | 管理现场的物理安全性。 数据中心进出人员的巡查，监控所有入口点。 针对在数据中心内部提供日常服务（餐饮、清洁）或 IT 工作的某些非特许人员，执行数据中心进出人员护送服务。 针对网络硬件展开例行监控和维护。 使用各种工具执行事件管理和中断修复工作。 针对数据中心内的物理硬件展开例行监控和维护。 根据业主的要求访问环境。 能够执行取证调查、记录事件报告，并提出强制性的安全培训和政策要求。 对关键安全工具（例如扫描仪和日志收集）拥有操作所有权和维护权。 | 对环境的持久性访问权限。 |
| Azure 事件会审（快速响应工程师） | 内部 | 有权访问客户数据 | 管理 MCIO、支持与工程团队之间的沟通。 会审平台事件、部署问题和服务请求。 | 环境的适时访问权限，对非客户系统的持久性访问权限有限。 |
| Azure 部署工程师 | 内部 | 有权访问客户数据 | 部署/升级平台组件、软件和有计划的配置更改，以支持 Azure。 | 环境的适时访问权限，对非客户系统的持久性访问权限有限。 |
| Azure 客户中断支持（租户） | 内部 | 有权访问客户数据 | 调试和诊断单个计算租户与 Azure 帐户出现的平台中断和故障。 分析故障。 为平台或客户推进关键修复措施，在整个支持团队中推进技术改进。 | 环境的适时访问权限，对非客户系统的持久性访问权限有限。 |
| Azure 现场工程师（监控工程师）和事件 | 内部 | 有权访问客户数据 | 使用诊断工具诊断和缓解平台运行状况。 推进批量发布的驱动程序的修复措施、修复中断时造成的问题，并为中断复原措施提供协助。 | 环境的适时访问权限，对非客户系统的持久性访问权限有限。 |
|Azure 客户 | 外部 | 不适用 | 不适用 | 不适用 |

Azure 使用唯一标识符对组织用户和客户（或代表组织用户执行操作的流程）进行身份验证。 这适用于 Azure 环境中包括的所有资产和设备。

### <a name="azure-internal-authentication"></a>Azure 内部身份验证

Azure 内部组件之间的通信受 TLS 加密的保护。 在大多数情况下，X.509 证书已自签名。 包含可从 Azure 网络外部访问的连接的证书以及 FC 的证书例外。 FC 具有 Microsoft 证书颁发机构 (CA) 颁发的证书，该 CA 以受信任的根 CA 为后盾。 因此，可以轻松滚动更新 FC 公钥。 此外，Microsoft 开发人员工具使用 FC 公钥。 当开发人员提交新的应用程序映像时，会使用 FC 公钥加密这些映像，以保护任何嵌入的机密。

### <a name="azure-hardware-device-authentication"></a>Azure 硬件设备身份验证

FC 维护一组凭据（密钥和/或密码），用于在其控制的各种硬件设备上对自身进行身份验证。 Microsoft 使用某个系统来防止访问这些凭据。 具体而言，在传输、保存和使用这些凭据时，可防止 Azure 开发人员、管理员和备份服务和人员访问敏感的机密信息或私人信息。

Microsoft 使用基于 FC 的主标识公钥的加密。 在设置 FC 和重新配置 FC 时将采用这种加密技术来传输用于访问网络硬件设备的凭据。 当 FC 需要凭据时，FC 会检索并解密凭据。

### <a name="network-devices"></a>网络设备

Azure 网络团队将配置网络服务帐户，使 Azure 客户端能够在网络设备（路由器、交换机和负载均衡器）中进行身份验证。

## <a name="secure-service-administration"></a>安全服务管理
Azure 运营人员必须使用安全管理员工作站 (SAW)。 客户可以使用特权访问工作站实现类似的控制。 借助 SAW，管理人员可以使用与用户的标准用户帐户不同的、单独分配的管理帐户。 SAW 通过为这些敏感帐户提供可信的工作站，建立此帐户分离做法。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何帮助保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [Azure 基础结构监视](azure-infrastructure-monitoring.md)
- [Azure 基础结构完整性](azure-infrastructure-integrity.md)
- [Azure 客户数据保护](azure-protection-of-customer-data.md)
