---
title: Azure Site Recovery 中的物理服务器灾难恢复体系结构
description: 本文概述了使用 Azure Site Recovery 服务将本地物理服务器灾难恢复到 Azure 的过程中使用的组件和体系结构。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: f2184654a8169cb353fb40fa76f0a7fe9b3df6f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87422651"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>物理服务器到 Azure 的灾难恢复体系结构

本文介绍了使用 [Azure Site Recovery](site-recovery-overview.md) 服务在本地站点与 Azure 之间对物理 Windows 和 Linux 服务器进行复制、故障转移和恢复时使用的体系结构和过程。

## <a name="architectural-components"></a>体系结构组件

下面的表和图提供了用于将物理服务器复制到 Azure 的组件的概要视图。

| **组件** | **要求** | **详细信息** |
| --- | --- | --- |
| **Azure** | Azure 订阅和 Azure 网络。 | 从本地物理计算机器复制的数据存储在 Azure 托管磁盘中。 运行从本地到 Azure 的故障转移时，将使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。 |
| **进程服务器** | 默认与配置服务器安装在一起。 | 充当复制网关。 接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。<br/><br/> 进程服务器还在要复制的服务器上安装移动服务。<br/><br/> 随着部署扩大，可以另外添加单独的进程服务器来处理更大的复制流量。 |
| **主目标服务器** | 默认与配置服务器安装在一起。 | 处理从 Azure 进行故障回复期间产生的复制数据。<br/><br/> 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。 |
| **复制的服务器** | 移动服务安装在复制的每台服务器上。 | 建议允许从进程服务器自动安装。 也可手动安装此服务，或者使用 Configuration Manager 等自动部署方法。 |

**物理机到 Azure 体系结构**

![组件](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>设置出站网络连接

若要使 Site Recovery 按预期工作，需修改出站网络连接以使环境进行复制。

> [!NOTE]
> Site Recovery 不支持使用身份验证代理来控制网络连接。

### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL：

| **名称**                  | 商用                               | 政府                                 | **说明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 存储                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`               | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| 复制               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允许 VM 与 Site Recovery 服务进行通信。 |
| 服务总线               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允许 VM 写入 Site Recovery 监视和诊断数据。 |


## <a name="replication-process"></a>复制过程

1. 创建部署，包括本地和 Azure 组件。 在恢复服务保管库中，指定复制源和目标，设置配置服务器，创建复制策略并启用复制。
1. 计算机使用复制策略进行复制，服务器数据的初始副本复制到 Azure 存储中。
1. 完成初始复制后，开始将增量更改复制到 Azure。 计算机的受跟踪更改保存在扩展名为 _.hrl_ 的文件中。
   - 计算机在 HTTPS 入站端口 443 上与配置服务器通信，进行复制管理。
   - 计算机在 HTTPS 入站端口 9443（可修改）上将复制数据发送到进程服务器。
   - 配置服务器通过 HTTPS 出站端口 443 与 Azure 协调复制管理。
   - 进程服务器从源计算机接收数据、优化和加密数据，然后通过 HTTPS 出站端口 443 将其发送到 Azure 存储。
   - 如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。 如果将多台计算机分组到复制组，并且这些组在故障转移时共享崩溃一致且应用一致的恢复点，请使用多 VM 方案。 如果计算机运行相同的工作负载并需要保持一致，则这些组非常有用。
1. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以使用 Azure ExpressRoute [公共对等互连](../expressroute/about-public-peering.md)。

   > [!NOTE]
   > 不支持使用站点到站点 VPN 通过本地站点或 Azure ExpressRoute [专用对等互连](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)进行复制。

**物理机到 Azure 的复制过程**

![复制过程](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

在设置复制后，可以运行故障恢复演练（测试性故障转移）来检查是否一切都符合预期。 然后，可以根据需要进行故障转移和故障回复。 请考虑以下事项：

- 不支持计划内故障转移。
- 必须故障回复到本地 VMware VM。 即使在将本地物理服务器复制到 Azure 的时候，也需要本地 VMware 基础结构。
- 对单台计算机进行故障转移，或者创建恢复计划来同时对多台计算机进行故障转移。
- 运行故障转移时，使用 Azure 存储中复制的数据创建 Azure VM。
- 触发初始故障转移之后，提交它即可开始访问 Azure VM 中的工作负载。
- 当本地主站点再次可用时，便可以故障回复。
- 设置故障回复基础结构，其中包括：
  - **Azure 中的临时进程服务器**：若要从 Azure 进行故障回复，需要设置用作进程服务器的 Azure VM，以处理从 Azure 进行的复制。 故障回复完成后，可以删除此 VM。
  - **VPN 连接**：若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。
  - **单独的主目标服务器**：默认情况下，故障回复由本地 VMware VM 上与配置服务器一起安装的主目标服务器处理。 如需对大量流量进行故障回复，应设置独立的本地主目标服务器。
  - **故障回复策略**：若要复制回到本地站点，需要创建故障回复策略。 此策略是在创建从本地到 Azure 的复制策略时自动创建的。
  - **VMware 基础结构**：若要进行故障回复，需要 VMware 基础结构。 不能故障回复到物理服务器。
- 组件就位后，故障回复分三个阶段进行：
  - **第 1 阶段**：重新保护 Azure VM，以便它们可以从 Azure 复制回本地 VMware VM。
  - **第 2 阶段**：运行到本地站点的故障转移。
  - **第 3 阶段**：在工作负荷进行故障回复后，重新启用复制。

**从 Azure 进行 VMware 故障回复**

![故障回复](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>后续步骤

若要设置从物理服务器到 Azure 的灾难恢复，请参阅[操作指南](physical-azure-disaster-recovery.md)。
