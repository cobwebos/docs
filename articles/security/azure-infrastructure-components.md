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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101398"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure 信息系统的组件和边界
本文提供有关 Microsoft Azure 体系结构和管理的一般说明。 Azure 系统环境由以下网络组成：

- Microsoft Azure 生产网络（Azure 网络）
- Microsoft 企业网络（Corpnet 网络）

独立的 IT 团队负责操作和维护 Azure 网络与 CorpNet 网络。

## <a name="azure-architecture"></a>Azure 体系结构
Microsoft Azure 是一个云计算平台和基础结构，用于通过 Microsoft 托管数据中心的网络构建、部署和管理应用程序与服务。 基于客户指定的资源数量，Azure 将会根据资源需求创建 VM。 这些 VM 在 Microsoft Azure 虚拟机监控程序中运行，该程序只能在云中使用，而不会提供给公众访问。

在每个 Azure 物理服务器节点上，有一个虚拟机监控程序直接通过硬件运行。 虚拟机监控程序将节点划分为数量可变的来宾虚拟机 (VM)。 每个节点还有一个特殊的“根”VM，用于运行主机 OS。 每个 VM 上已启用 Windows 防火墙。 只有开放的且可在内部或外部寻址的端口，才是客户配置的服务定义文件中显式定义的端口。 发往磁盘和网络的所有流量以及对其的访问均由虚拟机监控程序和根 OS 进行调解。

在主机层，Azure VM 运行最新 Windows Server 的定制强化版本。 Microsoft Azure 使用 Windows Server 的精简版本，其中只包含托管 VM 所需的组件。 这样既可以提高性能，又能减小受攻击面。 机器边界由虚拟机监控程序实施，不依赖于操作系统安全性。

**通过结构控制器 (FC) 进行 Azure 管理**：在 Azure 中，物理服务器（刀片服务器/节点）上运行的 VM 分组为大约由 1000 个 VM 构成的“群集”。 这些 VM 由一个横向扩展的冗余平台软件组件（称为 FC）单独管理。

每个 FC 管理其群集中运行的应用程序的生命周期，预配并监视受其控制的硬件的运行状况。 它会执行这两项自主操作，例如，在确定服务器出现故障时，它会在正常的服务器上 重建 VM 实例。 FC 还会执行应用程序管理操作，例如部署、更新和横向扩展应用程序。

数据中心划分为群集。 群集在 FC 级别隔离故障，并防止特定种类的错误影响到发生这些错误的群集以外的服务器。 为特定 Azure 群集提供服务的 FC 分组到 FC 群集。

**硬件库存**：Azure 硬件和网络设备的库存在进行启动配置的过程中准备，并在 datacenter.xml 配置文件中阐述。 进入 Azure 生产环境的任何新硬件和网络组件必须遵循启动配置过程。 FC 负责管理 datacenter.xml 配置文件中列出的整个库存。

**FC 托管的 OS**：OS 团队以虚拟硬盘 (VHD) 的形式提供 OS 映像，这些映像将部署到 Azure 生产环境中的所有主机和来宾 VM。 OS 团队通过自动化的脱机生成过程构建这些“基本映像”。 基本映像是操作系统的一个版本，其中的内核和其他核心组件已经过修改和优化，可支持 Azure 环境。

有三种类型的结构托管 OS 映像：

- 主机 OS – 主机 OS 是在主机 VM 上运行的定制操作系统
- 本机 OS – 在租户（例如 Azure 存储）上运行的、不使用任何虚拟机监控程序的本机 OS
- 来宾 OS – 在来宾 VM 上运行的来宾 OS

主机和本机 FC 托管的操作系统只能在云中使用，不可供公众访问。

**主机和本机 OS**：主机 OS 和本机 OS 是强化的 OS 映像，它们托管结构代理 (FA)，并在计算节点（作为节点上的第一个 VM 运行）和存储节点上运行。 使用主机和本机 OS 的优化基本映像的好处在于，可以减少 API 公开的外围应用或者存在较高安全风险的未使用组件，并增大 OS 的覆盖范围。 覆盖范围减小的这些操作系统只包括 Azure 所需的组件。 这可以提高性能，并减小受攻击面。

**来宾 OS**：来宾 OS VM 上运行的 Azure 内部组件不能像外部客户一样运行远程桌面协议 (RDP)。 对基线配置设置所做的任何更改都需要经历更改和发布管理过程。

## <a name="azure-datacenters"></a>Azure 数据中心
Microsoft 云基础结构和运营 (MCIO) 团队管理所有 Microsoft 联机服务的 Microsoft 物理基础结构和数据中心设施。 MCIO 主要负责管理数据中心内的物理和环境控制，以及管理和支持外围网络设备（边缘路由器和数据中心路由器）。 MCIO 还负责在数据中心内的机架上设置最基本的服务器硬件。 客户无法直接与 Azure 交互。

## <a name="service-management--service-teams"></a>服务管理和服务团队
Azure 服务的支持由称作“服务团队”的多个工程小组来管理。 每个服务团队负责某个方面的 Azure 支持工作。 每个服务团队必须指派一名全天候工作的工程师，以调查和解决服务中的故障。 默认情况下，服务团队无法对 Azure 中运行的硬件进行实物接触。

服务团队负责支持：

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
所有 Azure 内部用户都有一种根据敏感级别分类的员工状态，该状态定义了相应用户对客户数据的访问权限（有访问权限或无访问权限）。 Microsoft 的员工（或合同工）被视为内部用户。 其他所有用户被视为外部用户。 下表描述了用户对 Azure 的特权（身份验证后的授权权限）：

| 角色 | 内部或外部 | 敏感级别 | 履行的授权特权和功能 | 访问类型
| --- | --- | --- | --- | --- |
| Azure 数据中心工程师 | 内部 | 无权访问客户数据 | 管理现场的物理安全性；数据中心进出人员的巡查，监控所有入口点；针对在数据中心内部提供日常服务（餐饮、清洁）或 IT 工作的某些非特许人员，执行数据中心进出人员护送服务；针对网络硬件展开例行监控和维护；使用各种工具执行事件管理和中断修复工作；针对数据中心内的物理硬件展开例行监控和维护；根据业主的要求访问环境。 能够执行取证调查、记录事件报告，并提出强制性的安全培训和政策要求；某些安全工具（例如扫描仪和日志收集）的操作所有权和维护。 | 对环境的持久性访问权限 |
| Microsoft Azure 事件会审（快速响应工程师） | 内部 | 有权访问客户数据 | 管理基础结构运营、支持与 Azure 工程团队之间的沟通；会审平台事件、部署问题和服务请求。 | 环境的适时访问权限 - 对非客户系统的持久性访问权限有限 |
| Microsoft Azure 部署工程师 | 内部 | 有权访问客户数据 | 执行部署/升级平台组件、软件和有计划的配置更改，以支持 Microsoft Azure。 | 环境的适时访问权限 - 对非客户系统的持久性访问权限有限 |
| Microsoft Azure 客户中断支持（租户） | 内部 | 有权访问客户数据 | 调试和诊断单个计算租户和 Microsoft Azure 帐户出现的平台中断与故障；分析故障并为平台/客户推进关键修复措施，在整个支持团队中推进技术改进。 | 环境的适时访问权限 - 对非客户系统的持久性访问权限有限 |
| Microsoft Azure 现场工程师（监控工程师）和事件 | 内部 | 有权访问客户数据 | 负责使用诊断工具诊断和缓解平台运行状况；推进批量发布的驱动程序的修复措施、修复中断时造成的问题，并为中断复原措施提供协助。 | 环境的适时访问权限 - 对非客户系统的持久性访问权限有限 |
|Microsoft Azure 客户 | 外部 | 不适用 | 不适用 | 不适用 |

Azure 使用唯一标识符对访问 Azure 环境中所有资产/设备的组织用户和客户（或代表组织用户执行操作的流程）进行身份验证。

**Microsoft Azure 内部身份验证**：Azure 内部组件之间的通信受 TLS 加密的保护。 在大多数情况下，X.509 证书已自签名。 使用可从 Azure 网络外部访问的连接的证书以及 FC 例外。 FC 具有 Microsoft 证书颁发机构 (CA) 颁发的证书，该 CA 以受信任的根 CA 为后盾。 因此，可以轻松滚动更新 FC 公钥。 此外，Microsoft 开发人员工具使用 FC 公钥；当开发人员提交新的应用程序映像时，会使用 FC 公钥加密这些映像，以保护任何嵌入的机密。

**Microsoft Azure 的硬件设备身份验证**：FC 维护一组凭据（密钥和/或密码），用于在其控制的各种硬件设备上对自身进行身份验证。 用于传输、保存和使用这些凭据的系统旨在防止 Azure 开发人员、管理员和备份服务/人员访问敏感的机密信息或私人信息。

在设置 FC 和重新配置 FC 时，将使用基于 FC 主标识公钥的加密来传输用于访问网络硬件设备的凭据。 需要凭据时，FC 会检索并解密凭据。

**网络设备**：Azure 网络团队将配置网络服务帐户，使 Microsoft Azure 客户端能够在网络设备（路由器、交换机和负载均衡器）中进行身份验证。

## <a name="secure-service-administration"></a>安全服务管理
Microsoft Azure 运营人员必须使用安全管理员工作站（SAW；客户可以使用特权访问工作站 (PAW) 实现类似的控制）。 SAW 方法将分开使用管理人员的管理员帐户和用户帐户，是一种获得广泛认可的推荐做法。 这种做法使用单独分配的管理帐户，将其与用户的标准用户帐户相分离。 SAW 通过为这些敏感帐户提供可信的工作站，建立此帐户分离做法。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [Azure 基础结构的完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)
