---
title: 使用 Azure Site Recovery 执行 VMware 到 Azure 的灾难恢复时的配置服务器要求 | Microsoft Docs
description: 本文介绍了为了使用 Azure Site Recovery 执行 VMware 到 Azure 的灾难恢复而部署配置服务器时的支持和要求。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 94f410b7bd3b7c2eb3d7d6a9316323092010338e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418331"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>VMware 到 Azure 的灾难恢复的配置服务器要求

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要部署本地配置服务器。

- 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。
- [详细了解](vmware-azure-architecture.md)配置服务器组件和流程。

## <a name="configuration-server-deployment"></a>配置服务器部署

为实现 VMware VM 到 Azure 的灾难恢复，你将配置服务器部署为 VMware VM。

- Site Recovery 提供了从 Azure 门户中下载 OVA 模板，并导入 vCenter Server 以设置配置服务器 VM。
- 使用 OVA 模板部署配置服务器时，VM 将根据本文中列出的要求自动进行编译。
- 我们强烈建议你使用 OVA 模板设置配置服务器。 但是，如果你要设置 VMware VM 的灾难恢复并且无法使用 OVA 模板，则可以使用[提供的这些说明](physical-azure-set-up-source.md)部署配置服务器。
- 如果为本地物理计算机到 Azure 的灾难恢复部署配置服务器，请遵循[本文](physical-azure-set-up-source.md)中的说明。 


## <a name="hardware-requirements"></a>硬件要求

组件  | 要求  
--- | ---
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
可用磁盘空间（进程服务器缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB

## <a name="software-requirements"></a>软件要求

组件  | 要求  
--- | ---
操作系统 | Windows Server 2012 R2 <br> Windows Server 2016
操作系统区域设置 | 美国英语
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置 

## <a name="network-requirements"></a>网络要求

组件  | 要求  
--- | --- 
IP 地址类型 | 静态 
Internet 访问权限 | 服务器需要访问这些 URL（直接或通过代理）： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF 还需要访问以下 URL： <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
端口 | 443（控制通道协调）<br>9443（数据传输） 
NIC 类型 | VMXNET3（如果配置服务器是 VMware VM）

## <a name="required-software"></a>所需软件

组件  | 要求  
--- | ---
VMware vSphere PowerCLI | 如果配置服务器在 VMware VM 上运行，则应安装 [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MYSQL | 应安装 MySQL。 可以手动安装，或者让 Site Recovery 进行安装。

## <a name="sizing-and-capacity-requirements"></a>大小和容量要求

下表汇总了配置服务器的容器要求。 如果要复制多个 VMware VM，则应查看[容量规划注意事项](site-recovery-plan-capacity-vmware.md)然后运行用于 VMWare 复制的 [Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)工具。 

组件  | 要求  
--- | ---

| CPU  | 内存  | 缓存磁盘  | 数据更改率  | 复制的计算机  |
| --- | --- | --- | --- | --- |
| 8 个 vCPU<br/><br/> 2 个插槽 * 4 个核心 \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 或更少 | 少于 100 台计算机 |
| 12 个 vCPU<br/><br/> 2 个插槽 * 6 个核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机 |
| 16 个 vCPU<br/><br/> 2 个插槽 * 8 个核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 到 200 台计算机 | 



## <a name="next-steps"></a>后续步骤
设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。
