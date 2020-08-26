---
title: 体系结构-VMware/物理灾难恢复到具有 Azure Site Recovery 的辅助站点
description: 本文概述使用 Azure Site Recovery 将本地 VMware VM 或物理 Windows/Linux 服务器灾难恢复到辅助 VMware 站点时使用的组件和体系结构。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 7c884ce839523706e67e4278f43e237e1a2b0580
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496961"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>将 VMware/物理服务器复制到辅助本地站点的体系结构

本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 设置将本地 VMware 虚拟机 (VM) 或物理 Windows/Linux 服务器复制（用于灾难恢复）、故障转移以及恢复到辅助 VMware 站点时使用的体系结构和过程。


## <a name="architectural-components"></a>体系结构组件

**区域** | 组件  | **详细信息**
--- | --- | ---
**Azure** | 使用 InMage Scout 部署此方案。 | 若要获取 InMage Scout，需要 Azure 订阅。<br/><br/> 创建恢复服务保管库后，可以下载 InMage Scout 并安装最新的更新，以设置部署。
**进程服务器** | 位于主站点 | 可以部署进程服务器来处理缓存、压缩和数据优化操作。<br/><br/> 它还可以将安装的统一代理推送到要保护的计算机。
**配置服务器** | 位于辅助站点 | 配置服务器可以通过管理网站或 vContinuum 控制台管理、配置和监视部署。
**vContinuum 服务器** | 可选。 安装在配置服务器所在的同一位置。 | 它提供一个控制台用于管理和监视受保护的环境。
**主目标服务器** | 位于辅助站点 | 主目标服务器保存复制的数据。 它从进程服务器接收数据，在辅助站点中创建副本地器，并保存数据保留点。<br/><br/> 需要的主目标服务器数目取决于要保护的计算机数目。<br/><br/> 如果希望故障转移到主站点，则主站点上也需要有一个主目标服务器。 在此服务器上安装统一代理。
**VMware ESX/ESXi 和 vCenter 服务器** |  VMs 托管在 ESX/ESXi 主机上。 主机通过 vCenter 服务器进行托管 | 需要使用 VMware 基础结构来复制 VMware VM。
**VM/物理服务器** |  安装在要复制的 VMware VM 和物理服务器上的统一代理。 | 该代理充当所有组件之间的通信提供程序。

## <a name="set-up-outbound-network-connectivity"></a>设置出站网络连接

要使 Site Recovery 按预期运行，你需要修改出站网络连接，以允许你的环境进行复制。

> [!NOTE]
> Site Recovery 不支持使用身份验证代理来控制网络连接。

### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL：

| **Name**                  | **商用**                               | **Government**                                 | **说明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 存储                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| 复制               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允许 VM 与 Site Recovery 服务进行通信。 |
| 服务总线               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允许 VM 写入 Site Recovery 监视和诊断数据。 |

## <a name="replication-process"></a>复制过程

1. 在每个站点（配置、进程、主目标）中设置组件服务器，并在要复制的计算机上安装统一代理。
2. 在初始复制之后，每台计算机上的代理会将增量复制更改发送到进程服务器。
3. 进程服务器将优化这些数据，并将其传输到辅助站点上的主目标服务器。 配置服务器将管理复制进程。

![显示将 VMware Vm 和物理服务器复制到辅助数据中心的图示](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>后续步骤

[设置](vmware-physical-secondary-disaster-recovery.md) VMware VM 和物理服务器到辅助站点的灾难恢复。
