---
title: Azure Migrate 中的 Hyper-v 评估/迁移支持
description: 了解支持 Azure Migrate 的 Hyper-v 评估/迁移。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 6562d3f15d080a3bbc54a9985c12eae5908a9980
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186661"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>用于 Hyper-V 评估和迁移的支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了用于评估和迁移本地 Hyper-v Vm 的支持设置和限制。



## <a name="hyper-v-scenarios"></a>Hyper-v 方案

下表汇总了 Hyper-v Vm 支持的方案。

**部署** | **详细信息***
--- | ---
**评估本地 Hyper-v Vm** | [设置](tutorial-prepare-hyper-v.md)第一次评估。<br/><br/> [运行](scale-hyper-v-assessment.md)大规模评估。
**将 Hyper-V VM 迁移到 Azure** | [尝试](tutorial-migrate-hyper-v.md)迁移到 Azure。

## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
Azure 权限 | 你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。
Hyper-V VM | 在单个项目中最多评估35000个 Hyper-v Vm。 Azure 订阅中可以有多个项目。 项目可以包括 VMware Vm 和 Hyper-v Vm，最高可达评估限制。
地理位置 | [查看](migrate-support-matrix.md#supported-geographies)支持的地理位置。


## <a name="assessment-hyper-v-host-requirements"></a>评估-Hyper-v 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **主机部署**       | Hyper-v 主机可以是独立的，也可以部署到群集中。 |
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 <br/> 或者，如果不想分配管理员权限，请创建本地或域用户帐户，并将用户添加到这些组-远程管理用户、Hyper-v 管理员和性能监视器用户。 |
| **主机操作系统** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 不能访问运行 Windows Server 2012 的 Hyper-V 主机上的 VM。 |
| **PowerShell 远程处理**   | 必须在每个主机上启用。 |
| **Hyper-v 副本**       | 如果使用 Hyper-v 副本（或具有具有相同 VM 标识符的多个 Vm），并使用 Azure Migrate 发现原始 Vm 和复制的 Vm，则 Azure Migrate 生成的评估可能不准确。 |


## <a name="assessment-hyper-v-vm-requirements"></a>评估-Hyper-v VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须在你评估的 vm 上运行，才能捕获操作系统信息。 |



## <a name="assessment-appliance-requirements"></a>评估-设备要求

对于评估，Azure Migrate 运行轻型设备来发现 Hyper-v Vm，并将 VM 元数据和性能数据发送到 Azure Migrate。 设备在 Hyper-v VM 上运行，并使用从 Azure 门户下载的压缩 Hyper-v VHD 进行设置。 下表总结了设备的要求。

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **设备部署**   |  将设备部署为 Hyper-v VM。<br/> Azure Migrate 提供的设备 VM 为 Hyper-v VM 版本5.0。<br/> Hyper-v 主机必须运行 Windows Server 2012 R2 或更高版本。<br/> 主机需要足够的空间来分配 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间，以及设备 VM 的外部交换机。<br/> 设备需要静态或动态 IP 地址以及 internet 访问。
| **Azure Migrate 项目**  |  设备可以与单个项目关联。<br/> 可以将任意数量的设备与单个项目相关联。<br/> 你最多可以在一个项目中评估35000个 Vm。
| **Hyper-V 主机**          | 一个设备最多可以连接到300个 Hyper-v 主机。
| **发现**              | 单个设备最多可以发现 5000 Vm。
| **评估组**       | 最多可以在一个组中添加35000台计算机。
| **评估**             | 在单个评估中，最多可以评估 35000 Vm。



## <a name="assessment-appliance-url-access"></a>评估-设备 URL 访问

若要评估 Vm，Azure Migrate 设备需要连接到 internet。

- 部署设备时，Azure Migrate 会对下表中汇总的 Url 进行连接性检查。
- 如果使用的是基于 URL 的代理，则允许访问表中的 Url，确保代理解析在查找 Url 时收到的任何 CNAME 记录。
- 如果有拦截代理，可能需要将服务器证书从代理服务器导入到设备。


**URL** | **详细信息**  
--- | ---
*.portal.azure.com | 导航到 Azure 门户
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | 登录到 Azure 订阅
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备到服务通信创建 Azure Active Directory 应用程序。
management.azure.com | 为设备到服务通信创建 Azure Active Directory 应用程序。
dc.services.visualstudio.com | 日志记录和监视
*.vault.azure.net | 在设备与服务之间通信时管理 Azure Key Vault 中的机密。
aka.ms/* | 允许访问称为 "链接"。
https://download.microsoft.com/download/* | 允许从 Microsoft 下载站点下载。



## <a name="assessment-port-requirements"></a>评估-端口要求

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**本** | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ``` https://<appliance-ip-or-name>:44368 ```<br/> 端口443、5671和5672上的出站连接将发现和性能元数据发送到 Azure Migrate。
**Hyper-v 主机/群集** | WinRM 端口5985（HTTP）和5986（HTTPS）上的入站连接，使用通用信息模型（CIM）会话拉取 Hyper-v Vm 的配置和性能元数据。

## <a name="migration-limitations"></a>迁移-限制
对于复制，最多可以选择10个 Vm。 如果要迁移更多计算机，请在10个组中进行复制。

## <a name="migration-hyper-v-host-requirements"></a>迁移-Hyper-v 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **主机部署**       | Hyper-v 主机可以是独立的，也可以部署到群集中。 |
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 |
| **主机操作系统** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>迁移-Hyper-v VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **权限**           | 需要在要评估的每个 Hyper-v VM 上都有管理员权限。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须在你评估的 vm 上运行，才能捕获操作系统信息。 |
| **Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 Azure Migrate 会自动对以下操作系统进行这些更改：<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8<br/><br/> 对于其他操作系统，需要在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。 |
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
| **IPv6** | 不受支持。
| **NIC 组合** | 不受支持。
| **Azure Site Recovery** | 如果 VM 启用了与 Azure Site Recovery 的复制，则无法使用 Azure Migrate Server 迁移进行复制。





## <a name="migration-hyper-v-host-url-access"></a>迁移-Hyper-v 主机 URL 访问

下表总结了 Hyper-v 主机的 URL 访问要求。

**URL** | **详细信息**  
--- | ---
login.microsoftonline.com | 使用 Active Directory 进行访问控制和标识管理。
*.backup.windowsazure.com | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.windows.com | 验证系统与全局时间之间的时间同步。

## <a name="migration-port-access"></a>迁移-端口访问

下表总结了用于 VM 迁移的 Hyper-v 主机和 Vm 上的端口要求。

**设备** | **Connection**
--- | ---
Hyper-v 主机/Vm | HTTPS 端口443上的出站连接，用于将 VM 复制数据发送到 Azure Migrate。




## <a name="next-steps"></a>后续步骤

[准备用于迁移的 HYPER-V VM 评估](tutorial-prepare-hyper-v.md)。
