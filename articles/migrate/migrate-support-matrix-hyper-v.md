---
title: 对于 HYPER-V 评估和迁移的 azure 迁移支持矩阵
description: 总结了设置和 HYPER-V 评估和使用 Azure Migrate 服务迁移限制。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811344"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>对于 HYPER-V 评估和迁移的支持矩阵

可以使用[Azure Migrate 服务](migrate-overview.md)来评估和将计算机迁移到 Microsoft Azure 云。 本文汇总了支持设置和用于评估和迁移的本地 HYPER-V Vm 的限制。



## <a name="hyper-v-scenarios"></a>HYPER-V 方案

表概述了支持的方案的 HYPER-V Vm。

**部署** | **详细信息*** 
--- | --- 
**评估的本地 HYPER-V Vm** | [设置](tutorial-prepare-hyper-v.md)第一个评估。<br/><br/> [运行](scale-hyper-v-assessment.md)大规模的评估。
**将 HYPER-V Vm 迁移到 Azure** | [试用](tutorial-migrate-hyper-v.md)迁移到 Azure。

    

## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
Azure 权限 | 需要参与者或所有者的权限的订阅中创建一个 Azure Migrate 项目。
Hyper-V VM | 评估最多 10,000 个单个项目中的 HYPER-V Vm。

项目可以包括 VMware Vm 和 HYPER-V 虚拟机，直到达到评估限制为止。


## <a name="assessment-hyper-v-host-requirements"></a>评估 Hyper V 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **主机部署**       | HYPER-V 主机可以是独立的或部署到群集中。 |
| **权限**           | 你需要在 HYPER-V 主机上的管理员权限。 |
| **主机操作系统** | Windows Server 2016 或 Windows Server 2012 R2。<br/> 您不能评估运行 Windows Server 2019 的 HYPER-V 主机上的 Vm。 |
| **PowerShell 远程处理**   | 必须在每个主机上启用。 |
| **Hyper-V Replica**       | 如果你使用 HYPER-V 副本 （或具有多个 Vm 使用相同的 VM 标识符），并发现这两个原始和已复制的 Vm 使用 Azure Migrate，生成的 Azure Migrate 评估可能不准确。 |


## <a name="assessment-hyper-v-vm-requirements"></a>评估 HYPER-V VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)并[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure 支持的操作系统。 |
| **权限**           | 你需要在你想要评估每个 HYPER-V 虚拟机上的管理员权限。 |
| **集成服务**       | [HYPER-V 集成服务](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须评估，以便捕获操作系统的信息的 Vm 上运行。 |
| **适用于 Azure 所需的更改** | 某些 Vm 可能需要更改，以便它们可以在 Azure 中运行。 Azure Migrate，可自动对以下操作系统的这些更改：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 对于其他操作系统，你需要进行迁移之前手动调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果 /boot 专用分区上，它应驻留在 OS 磁盘，并且不能跨越多个磁盘。<br/> 如果 /boot 是根 （/） 分区的一部分，/ 分区应在 OS 磁盘上并不跨其他磁盘。 |
| **UEFI 启动**                  | 具有 UEFI boot 的虚拟机不支持进行迁移。 |
| **加密的磁盘/卷**    | 使用加密的磁盘卷的虚拟机不支持进行迁移。 |
| **RDM/传递磁盘**      | 如果 Vm 有 RDM 或传递磁盘，则这些磁盘不会复制到 Azure。 |
| **NFS**                        | 不会复制在 Vm 上装载为卷的 NFS 卷。 |
| **目标磁盘**                | Azure Migrate 评估建议迁移到 Azure Vm 结合仅托管磁盘。 |


## <a name="assessment-appliance-requirements"></a>评估设备要求

评估时，Azure Migrate 运行发现的 HYPER-V Vm，并将 VM 元数据和性能数据发送到 Azure Migrate 的轻量设备。 设备运行的 HYPER-V VM 上，并设置使用从 Azure 门户下载的压缩的 HYPER-V VHD。 下表总结了设备要求。

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **Azure Migrate 项目**  |  一台设备可以与单个项目相关联。<br/> 可以使用单个设备发现最多 5000 个的 HYPER-V Vm。
| **HYPER-V 限制**    |  将设备部署为 HYPER-V VM。<br/> 设备提供的 VM 的 HYPER-V VM 版本 5.0。<br/> VM 主机必须运行 Windows Server 2012 R2 或更高版本。<br/> 它需要足够的空间来分配 16 GB RAM，4 个虚拟处理器和 1 外部交换机的设备的 VM。<br/> 设备需要静态或动态 IP 地址和 internet 访问权限。
| **Hyper-v 的目标设备**      |  设备设置为 HYPER-V VM。<br/> 可供下载的 VHD 是 HYPER-V 虚拟机版本 5.0。
| **主机**                   | 运行设备 VM 的 VM 主机必须运行 Windows Server 2012 R2 或更高版本。<br/> 它需要足够的空间来为设备 VM 分配 16 GB RAM、 4 个虚拟处理器和一台外部交换机。<br/> 设备需要静态或动态 IP 地址和 internet 访问权限。 |
| **迁移支持**      | 若要开始将复制计算机，在设备上的迁移网关服务必须是 1.18.7141.12919 或更高版本。 登录到设备的 web 应用来检查版本。 |

## <a name="assessment-appliance-url-access"></a>评估设备 URL 访问

若要评估的 Vm，Azure Migrate 设备需要 internet 连接。

- 在部署设备时，Azure Migrate 执行到下表中汇总的 Url 的连接性检查。
- 如果您使用基于 URL 的 firewall.proxy，在表中，确保代理解析任何 CNAME 记录接收到查找 Url 时允许访问的 Url。
- 如果您有截取代理，你可能需要从代理服务器的服务器证书导入到该设备。 

    
**URL** | **详细信息**  
--- | --- 
*.portal.azure.com | 导航到 Azure 门户
*.windows.net | 登录到 Azure 订阅
*.microsoftonline.com | 创建的 Azure Active Directory 应用程序到服务通信的设备。
management.azure.com | 创建的 Azure Active Directory 应用程序到服务通信的设备。
dc.services.visualstudio.com | 日志记录和监视 
*.vault.azure.net | 在设备和服务之间进行通信时，请管理 Azure 密钥保管库中的机密。


## <a name="assessment-port-requirements"></a>评估端口要求

下表总结了有关评估端口要求。

**设备** | **Connection**
--- | --- 
**Appliance** | TCP 端口 3389，以允许远程桌面连接到设备上的入站的连接。<br/> 入站端口 44368 远程访问设备管理应用程序使用的 URL 上的连接： https://<appliance-ip-or-name>:44368<br/> 要将发现和性能元数据发送到 Azure Migrate 的端口 443 的出站连接。
**HYPER-V 主机/群集** | 入站 WinRM 端口 5985 (HTTP) 和 5986(https (HTTPS) 进行拉取配置和性能元数据的使用通用信息模型 (CIM) 会话在 HYPER-V 虚拟机上的连接。

## <a name="migration-hyper-v-host-requirements"></a>迁移 Hyper V 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **主机部署**       | HYPER-V 主机可以是独立的或部署到群集中。 |
| **权限**           | 你需要在 HYPER-V 主机上的管理员权限。 |
| **主机操作系统** | Windows Server 2019、 Windows Server 2016 中或 Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>迁移 HYPER-V VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)并[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure 支持的操作系统。 |
| **权限**           | 你需要在你想要评估每个 HYPER-V 虚拟机上的管理员权限。 |
| **集成服务**       | [HYPER-V 集成服务](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须评估，以便捕获操作系统的信息的 Vm 上运行。 |
| **适用于 Azure 所需的更改** | 某些 Vm 可能需要更改，以便它们可以在 Azure 中运行。 Azure Migrate，可自动对以下操作系统的这些更改：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 对于其他操作系统，你需要进行迁移之前手动调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果 /boot 专用分区上，它应驻留在 OS 磁盘，并且不能跨越多个磁盘。<br/> 如果 /boot 是根 （/） 分区的一部分，/ 分区应在 OS 磁盘上并不跨其他磁盘。 |
| **UEFI 启动**                  | 具有 UEFI boot 的虚拟机不支持进行迁移。 |
| **加密的磁盘/卷**    | 使用加密的磁盘卷的虚拟机不支持进行迁移。 |
| **RDM/传递磁盘**      | 如果 Vm 有 RDM 或传递磁盘，则这些磁盘不会复制到 Azure。 |
| **NFS**                        | 不会复制在 Vm 上装载为卷的 NFS 卷。 |
| **目标磁盘**                | 你可以使用仅限托管磁盘迁移到 Azure Vm。 |




## <a name="migration-hyper-v-host-url-access"></a>迁移 Hyper V 主机 URL 访问

下表总结了 HYPER-V 主机的 URL 访问要求。

**URL** | **详细信息**  
--- | ---
login.microsoftonline.com | 使用 Active Directory 访问控制和标识管理。
*.backup.windowsazure.com | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。
dc.services.visualstudio.com | 上传用于内部监视的应用程序日志。
time.windows.com | 验证系统与全球时间之间的时间同步。

## <a name="migration-port-access"></a>迁移端口的访问

下表总结了在 HYPER-V 主机和 Vm 上的 VM 迁移的端口要求。

**设备** | **Connection**
--- | --- 
HYPER-V 主机/Vm | HTTPS 上的出站连接的端口 443，用于将 VM 的复制数据发送到 Azure Migrate。

  
## <a name="migration-vm-disk-support"></a>迁移 VM 的磁盘支持 

**支持** | **详细信息**
--- | ---
已迁移的磁盘 | 虚拟机只能迁移到 Azure 中的托管磁盘 (标准 HHD，高级 SSD)。
   

## <a name="next-steps"></a>后续步骤

[为 HYPER-V VM 评估准备](tutorial-prepare-hyper-v.md)进行迁移。




 
