---
title: Azure Migrate 中的 Hyper-v 迁移支持
description: 了解支持 Azure Migrate 的 Hyper-v 迁移。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245819"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-v 迁移的支持矩阵

本文汇总了有关迁移 Hyper-v Vm 与[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)的支持设置和限制。 如果正在寻找有关评估要迁移到 Azure 的 Hyper-v Vm 的信息，请查看[评估支持矩阵](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>迁移限制

对于复制，最多可以选择10个 Vm。 如果要迁移更多计算机，请在10个组中进行复制。


## <a name="hyper-v-hosts"></a>Hyper-v 主机

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-v 主机可以是独立的，也可以部署到群集中。 <br/>需要在 Hyper-v 主机上安装 Azure Migrate 复制软件（Hyper-v 复制提供程序）。|
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 |
| **主机操作系统** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。 |
| **URL 访问** | Hyper-v 主机上的复制提供程序软件将需要访问以下 URL：<br/><br/> -login.microsoftonline.com：使用 Active Directory 访问控制和标识管理。<br/><br/> -*. backup.windowsazure.com：复制数据传输和协调。 迁移服务 Url。<br/><br/> -*. blob.core.windows.net：将数据上传到存储帐户。<br/><br/> -dc.services.visualstudio.com：上传用于内部监视的应用日志。<br/><br/> -time.windows.com：验证系统与全局时间之间的时间同步。
| **端口访问** |  HTTPS 端口443上的出站连接，用于发送 VM 复制数据。

## <a name="hyper-v-vms"></a>Hyper-V VM

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 需要在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果/boot 位于专用分区上，则它应驻留在 OS 磁盘上，而不会分布在多个磁盘上。<br/> 如果/boot 是根（/）分区的一部分，则 "/" 分区应在 OS 磁盘上，而不是在其他磁盘上。 |
| **UEFI 启动**                  | Azure 中迁移的 VM 将自动转换为 BIOS 启动 VM。 VM 应仅运行 Windows Server 2012 和更高版本。 OS 磁盘最多可以有5个分区或更少，操作系统磁盘的大小应小于 300 GB。
  |
| **磁盘大小**                  | 对于 OS 磁盘为 2 TB，数据磁盘为 4 TB。
| **磁盘编号** | 每个 VM 最多16个磁盘。
| **加密磁盘/卷**    | 不支持迁移。 |
| **RDM/传递磁盘**      | 不支持迁移。 |
| **共享磁盘** | 使用共享磁盘的 Vm 不支持迁移。
| **NFS**                        | 不会复制装载为 Vm 上的卷的 NFS 卷。 |
| **/ISCSI**                      | 具有 iSCSI 目标的 Vm 不支持迁移。
| **目标磁盘**                | 仅可将 Azure Vm 迁移到托管磁盘。 |
| **IPv6** | 不支持。
| **NIC 组合** | 不支持。
| **Azure Site Recovery** | 如果 VM 启用了与 Azure Site Recovery 的复制，则无法使用 Azure Migrate Server 迁移进行复制。
| **端口** | HTTPS 端口443上的出站连接，用于发送 VM 复制数据。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 Vm 必须满足此表中汇总的 Azure VM 要求。

组件 | **要求** | **详细信息**
--- | --- | ---
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 16或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
迁移后连接-Windows | 若要在迁移后连接到运行 Windows 的 Azure Vm：<br/> -迁移之前，在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” **“允许的应用”中针对所有配置文件允许 RDP** > 。<br/> 对于站点到站点 VPN 访问，在**Windows 防火墙**中启用 rdp 和允许 Rdp -> 允许用于**域和专用**网络的**应用和功能**。 此外，请检查操作系统的 SAN 策略是否设置为**OnlineAll**。 [了解详细信息](prepare-for-migration.md)。 |
迁移后连接-Linux | 使用 SSH 迁移后连接到 Azure Vm：<br/> 在迁移之前，请在本地计算机上检查安全外壳服务是否设置为 "启动"，以及防火墙规则是否允许 SSH 连接。<br/> 故障转移后，在 Azure VM 上，允许已故障转移的 VM 上的网络安全组和连接到的 Azure 子网的 SSH 端口建立传入连接。 此外，为 VM 添加公共 IP 地址。 |  

## <a name="next-steps"></a>后续步骤

迁移用于迁移的[Hyper-v vm](tutorial-migrate-hyper-v.md) 。
