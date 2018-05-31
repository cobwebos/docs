---
title: include 文件
description: include 文件
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901289"
---
**配置/进程服务器要求**

**组件** | **要求** 
--- | ---
**硬件** | 
**CPU 核心数** | 8 
**RAM** | 16 GB
**磁盘数目** | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
**可用磁盘空间（进程服务器缓存）** | 600 GB
**可用磁盘空间（保留磁盘）** | 600 GB
**软件** | 
**操作系统** | Windows Server 2012 R2 <br> Windows Server 2016
**操作系统区域设置** | 美国英语
**Windows Server 角色** | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
**组策略** | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**IIS** | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置 
**网络** | 
**IP 地址类型** | 静态 
**Internet 访问权限** | 服务器需要访问这些 URL（直接或通过代理） <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi（如果要设置配置服务器） <br> - time.nist.gov <br> - time.windows.com 
**端口** | 443（控制通道协调）<br>9443（数据传输） 
**VMWARE（将配置/进程服务器设置为 VMware VM 时）**
**NIC 类型** | VMXNET3  
**在 VM 上运行的 VMware vSphere PowerCLI* | [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**配置/进程服务器大小要求**

**CPU** | **内存** | **缓存磁盘** | **数据更改率** | **复制的计算机**
--- | --- | --- | --- | ---
8 个 vCPU<br/><br/> 2 个套接字 * 4 个内核 @ 2.5 GHz | 16GB | 300 GB | 500 GB 或更少 | < 100 台计算机
12 个 vCPU<br/><br/> 2 个套接字 * 6 个内核 @ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机
16 个 vCPU<br/><br/> 2 个套接字 * 8 个内核 @ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 台计算机

