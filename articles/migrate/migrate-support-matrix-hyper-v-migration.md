---
title: 支持 Azure 迁移中的超 V 迁移
description: 了解使用 Azure 迁移对 Hyper-V 迁移的支持。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538182"
---
# <a name="support-matrix-for-hyper-v-migration"></a>支持超 V 迁移矩阵

本文总结了使用[Azure 迁移：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)迁移超级 VM 的支持设置和限制。 如果要查找有关评估 Hyper-V VM 以迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>迁移限制

一次最多可以选择 10 个 VM 进行复制。 如果要迁移更多计算机，以 10 的组进行复制。


## <a name="hyper-v-hosts"></a>Hyper-V 主机

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-V 主机可以是独立的，也可以部署在群集中。 <br/>Azure 迁移复制软件（Hyper-V 复制提供程序）安装在 Hyper-V 主机上。|
| **权限**           | 您需要对 Hyper-V 主机的管理员权限。 |
| **主机操作系统** | Windows 服务器 2019、Windows 服务器 2016 或 Windows 服务器 2012 R2。 |
| **端口访问** |  HTTPS 端口 443 上的出站连接以发送 VM 复制数据。

### <a name="url-access-public-cloud"></a>URL 访问（公共云）

Hyper-V 主机上的复制提供程序软件将需要访问这些 URL。

**URL** | **详细信息**
--- | ---
login.microsoftonline.com | 使用活动目录进行访问控制和标识管理。
backup.windowsazure.com | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.com | 用于迁移。
\* .blob.core.windows.net | 将数据上载到存储帐户。 
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.windows.com | 验证系统与全局时间之间的时间同步。

### <a name="url-access-azure-government"></a>URL 访问（Azure 政府）

Hyper-V 主机上的复制提供程序软件将需要访问这些 URL。

**URL** | **详细信息**
--- | ---
login.microsoftonline.us | 使用活动目录进行访问控制和标识管理。
backup.windowsazure.us | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.us | 用于迁移。
*.blob.core.usgovcloudapi.net | 将数据上载到存储帐户。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.nist.gov | 验证系统与全局时间之间的时间同步。


## <a name="hyper-v-vms"></a>Hyper-V VM

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果 /boot 位于专用分区上，则应驻留在操作系统磁盘上，并且不应跨多个磁盘分布。<br/> 如果 /boot 是根 （/） 分区的一部分，则'/' 分区应位于 OS 磁盘上，而不是跨其他磁盘。 |
| **UEFI 启动**                  | Azure 中迁移的 VM 将自动转换为 BIOS 引导 VM。 VM 应仅运行 Windows Server 2012 及更高版本。 OS 磁盘应最多具有五个或更少的分区，并且 OS 磁盘的大小应小于 300 GB。
  |
| **磁盘大小**                  | 2 TB 用于 OS 磁盘，4 TB 用于数据磁盘。
| **磁盘编号** | 每个 VM 最多 16 个磁盘。
| **加密磁盘/卷**    | 不支持迁移。 |
| **RDM/直通磁盘**      | 不支持迁移。 |
| **共享磁盘** | 迁移不支持使用共享磁盘的 VM。
| **NFS**                        | 在 VM 上装载的卷的 NFS 卷不会复制。 |
| **ISCSI**                      | 不支持具有 iSCSI 目标的 VM 进行迁移。
| **目标磁盘**                | 只能迁移到具有托管磁盘的 Azure VM。 |
| **IPv6** | 不支持。
| **NIC 团队** | 不支持。
| **Azure Site Recovery** | 如果启用 VM 进行 Azure 站点恢复复制，则无法使用 Azure 迁移服务器迁移进行复制。
| **端口** | HTTPS 端口 443 上的出站连接以发送 VM 复制数据。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 VM 必须满足此表中总结的 Azure VM 要求。

**组件** | **要求** | **详细信息**
--- | --- | ---
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 16 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
迁移后连接 - Windows | 要在迁移后连接到运行 Windows 的 Azure VM，请执行：<br/> - 在迁移在本地 VM 上启用 RDP 之前。 确保为**公共**配置文件添加了 TCP 和 UDP 规则，并且所有配置文件都允许在 Windows**防火墙** > **允许应用**中使用 RDP。<br/> 对于站点到站点 VPN 访问，请在**Windows 防火墙** -> **允许的应用和功能**中启用 RDP。 **Domain and Private** 此外，请检查操作系统的 SAN 策略是否设置为**OnlineAll**。 [了解详细信息](prepare-for-migration.md)。 |
迁移后连接 -Linux | 要使用 SSH 在迁移后连接到 Azure VM，<br/> 在迁移之前，在本地计算机上，检查安全外壳服务是否设置为"开始"，并且防火墙规则是否允许 SSH 连接。<br/> 故障转移后，在 Azure VM 上，允许传入连接到 SSH 端口，用于通过 VM 故障的网络安全组规则以及连接到它的 Azure 子网。 此外，为 VM 添加公共 IP 地址。 |  

## <a name="next-steps"></a>后续步骤

[迁移用于迁移的超 VM。](tutorial-migrate-hyper-v.md)
