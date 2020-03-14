---
title: Azure Migrate 复制设备
description: 了解基于代理的 VMWare 迁移的 Azure Migrate 复制设备。
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245858"
---
# <a name="replication-appliance"></a>复制设备

本文介绍了使用基于代理的迁移将 VMware Vm、物理计算机和私有/公有云 Vm 迁移到 Azure 时[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具使用的复制设备。 


## <a name="overview"></a>概述

在设置 VMware Vm 或物理服务器的基于代理的迁移时，将部署复制设备。 它部署为一个本地计算机，作为 VMware VM 或物理服务器。 它运行：

- **复制设备**：复制设备用于协调本地 VMware vm 和物理服务器复制到 Azure 时的通信并管理数据复制。
- **进程服务器**：默认情况下安装在复制设备上的进程服务器，并执行以下操作：
    - **复制网关**：充当复制网关。 它从启用了复制的计算机接收复制数据。 它通过缓存、压缩和加密来优化复制数据，并将其发送到 Azure。
    - **代理安装程序**：执行移动服务的推送安装。 必须在要复制以进行迁移的每台本地计算机上安装和运行此服务。

## <a name="appliance-deployment"></a>设备部署

**用于** | **详细信息**
--- |  ---
基于 VMware VM 代理的迁移 | 从 Azure Migrate 中心下载 .OVA 模板，并导入到 vCenter Server 以创建设备 VM。
基于物理计算机代理的迁移 | 如果没有 VMware 基础结构，或者无法使用 .OVA 模板创建 VMware VM，请从 Azure Migrate 集线器下载软件安装程序，并运行它以设置设备计算机。

## <a name="appliance-requirements"></a>设备要求

当使用 Azure Migrate 中心提供的 .OVA 模板设置复制设备时，设备将运行 Windows Server 2016 并符合支持要求。 如果在物理服务器上手动设置复制设备，请确保它符合要求。

组件 | **要求**
--- | ---
 | **VMware VM 设备**
PowerCLI | 如果复制设备在 VMware VM 上运行，则应安装[PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 。
NIC 类型 | VMXNET3 （如果设备是 VMware VM）
 | **硬件设置**
CPU 核心数 | 8
RAM | 16 GB
磁盘数目 | 三：操作系统磁盘、进程服务器缓存磁盘和保留驱动器。
可用磁盘空间（缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
**软件设置** |
操作系统 | Windows Server 2016 或 Windows Server 2012 R2
许可 | 该设备附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。<br/><br/> 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。
操作系统区域设置 | 美国英语
TLS | 应启用 TLS 1.2。
.NET Framework | 应在计算机上安装 .NET Framework 4.6 或更高版本（启用了强密码。
MySQL | MySQL 应安装在设备上。<br/> 应安装 MySQL。 可以手动安装，也可以在设备部署期间安装 Site Recovery。
其他应用 | 请勿在复制设备上运行其他应用。
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置
**网络设置** |
IP 地址类型 | 静态
端口 | 443（控制通道协调）<br>9443（数据传输）
NIC 类型 | VMXNET3

## <a name="mysql-installation"></a>MySQL 安装 

必须在复制设备计算机上安装 MySQL。 可以使用以下方法之一来安装它。

**方法** | **详细信息**
--- | ---
手动下载和安装 | 下载 MySQL 应用程序 & 将其放在 C:\Temp\ASRSetup 文件夹中，然后手动安装。<br/> 设置设备 MySQL 时，将显示 "已安装"。
无在线下载 | 将 MySQL 安装程序应用程序放在 C:\Temp\ASRSetup. 文件夹中。 安装设备并单击下载并安装 MySQL 时，安装程序将使用所添加的安装程序。
下载并安装到 Azure Migrate | 当你安装设备并提示你安装 MySQL 时，请选择 "**下载并安装**"。

## <a name="url-access"></a>URL 访问

复制设备需要对这些 Url 的访问权限。

**URL** | **详细信息**
--- | ---
\*.backup.windowsazure.com | 用于复制的数据传输和协调
\*.store.core.windows.net | 用于复制的数据传输和协调
\*.blob.core.windows.net | 用于访问存储所复制数据的存储帐户
\*.hypervrecoverymanager.windowsazure.com | 用于复制管理操作和协调
https:\//management.azure.com | 用于复制管理操作和协调
*.services.visualstudio.com | 用于遥测数据（可选）
time.nist.gov | 用于检查系统时间与全球时间之间的时间同步。
time.windows.com | 用于检查系统时间与全球时间之间的时间同步。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https：\//login.live.com <br/> https：\//graph.windows.net <br/> https:\//login.windows.net <br/> https：\//www.live.com <br/> https：\//www.microsoft.com  | OVF 安装程序需要对这些 Url 的访问权限。 它们由 Azure Active Directory 用于访问控制和标识管理
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下载

## <a name="port-access"></a>端口访问

**设备** | **Connection**
--- | ---
VM | Vm 上运行的移动服务与用于复制管理的端口 HTTPS 443 入站上的本地复制设备（配置服务器）进行通信。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备通过端口 HTTPS 443 出站来协调与 Azure 的复制。
进程服务器 | 进程服务器接收复制数据，对其进行优化并对其进行加密，并通过端口443出站发送到 Azure 存储。<br/> 默认情况下，进程服务器在复制设备上运行。


## <a name="replication-process"></a>复制过程

1. 启用 VM 复制时，对 Azure 存储的初始复制将开始（使用指定复制策略）。 
2. 流量通过 Internet 复制到 Azure 存储公共终结点。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 初始复制完成后，增量复制将会开始。 已记录对计算机所做的更改。
4. 通信按如下方式发生：
    - Vm 与用于复制管理的端口 HTTPS 443 入站上的复制设备通信。
    - 复制设备通过端口 HTTPS 443 出站来协调与 Azure 的复制。
    - Vm 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在复制设备上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据在 Azure 中的缓存存储帐户中记录第一个土地。 将处理这些日志，并将数据存储在 Azure 托管磁盘中。

![体系结构](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>设备升级

设备将从 Azure Migrate 中心手动升级。 建议始终运行最新版本。

1. 在 Azure Migrate > 服务器 > Azure Migrate：服务器评估、基础结构服务器，请单击 "**配置服务器**"。
2. 在**配置服务器**中，当复制设备的新版本可用时，**代理版本**中会出现一个链接。 
3. 将安装程序下载到复制设备计算机，然后安装升级。 安装程序将检测设备上当前正在运行的版本。
 
## <a name="next-steps"></a>后续步骤

- [了解如何](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance)为基于代理的 VMware VM 迁移设置复制设备。
- [了解如何](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)为物理服务器设置复制设备。
