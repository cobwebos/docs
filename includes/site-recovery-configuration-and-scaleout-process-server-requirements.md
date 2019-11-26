---
title: include 文件
description: include 文件
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 7baa2dbd1583ebbccbf9b21df3531404bd839e10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260661"
---
**配置和进程服务器要求**


## <a name="hardware-requirements"></a>硬件要求

**组件** | **要求** 
--- | ---
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
可用磁盘空间（进程服务器缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
 | 

## <a name="software-requirements"></a>软件要求

**组件** | **要求** 
--- | ---
操作系统 | Windows Server 2012 R2 <br> Windows Server 2016
操作系统区域设置 | 美国英语
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置 
FIPS （联邦信息处理标准） | 不要启用 FIPS 模式
|

## <a name="network-requirements"></a>网络要求

**组件** | **要求** 
--- | --- 
IP 地址类型 | Static 
端口 | 443（控制通道协调）<br>9443（数据传输） 
NIC 类型 | VMXNET3 （如果配置服务器是 VMware VM）
 |
**Internet 访问**（服务器需要直接或通过代理访问以下 url）：|
\*.backup.windowsazure.com | 用于复制的数据传输和协调
\*.store.core.windows.net | 用于复制的数据传输和协调
\*.blob.core.windows.net | 用于访问存储所复制数据的存储帐户
\*.hypervrecoverymanager.windowsazure.com | 用于复制管理操作和协调
https:\//management.azure.com | 用于复制管理操作和协调 
*.services.visualstudio.com | 用于遥测目的（可选）
time.nist.gov | 用于检查系统时间与全球时间之间的时间同步
time.windows.com | 用于检查系统时间与全球时间之间的时间同步
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https：\//login.live.com </li><li> https：\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https：\//www.live.com </li><li> https：\//www.microsoft.com </li></ul> | OVF 安装程序需要对这些 Url 的访问权限。 它们用于 Azure Active Directory 的访问控制和标识管理。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | 完成 MySQL 下载。 </br> 在少数几个区域中，下载可能会被重定向到 CDN URL。 如果需要，请确保 CDN URL 还列入允许列表。
|

## <a name="required-software"></a>所需软件

**组件** | **要求** 
--- | ---
VMware vSphere PowerCLI | 如果配置服务器在 VMware VM 上运行，则应安装 [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MYSQL | 应安装 MySQL。 可以手动安装，或者让 Site Recovery 进行安装。 （有关详细信息，请参阅[配置设置](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)）
|

## <a name="sizing-and-capacity-requirements"></a>大小和容量要求

下表汇总了配置服务器的容器要求。 如果要复制多个 VMware Vm，请查看[容量规划注意事项](../articles/site-recovery/site-recovery-plan-capacity-vmware.md)，并运行[Azure Site Recovery 部署规划器工具](../articles/site-recovery/site-recovery-deployment-planner.md)。


**CPU** | **内存** | **缓存磁盘** | **数据更改率** | **复制的计算机**
--- | --- | --- | --- | ---
8 个 vCPU<br/><br/> 2 个插槽 * 4 个核心 \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 或更少 | < 100 台计算机
12 个 vCPU<br/><br/> 2 个插槽 * 6 个核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机
16 个 vCPU<br/><br/> 2 个插槽 * 8 个核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 台计算机
|

