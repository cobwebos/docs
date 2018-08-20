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
ms.openlocfilehash: ade53ba29d165b3b33ef25dabda25c4e60022608
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133817"
---
配置/进程服务器要求

组件 | 要求 
--- | ---
硬件设置 | 
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
可用磁盘空间（进程服务器缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
 | 
软件设置 | 
操作系统 | Windows Server 2012 R2 <br> Windows Server 2016
操作系统区域设置 | 美国英语
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置 
| 
网络设置 | 
IP 地址类型 | 静态 
Internet 访问权限 | 服务器需要访问这些 URL（直接或通过代理）： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF 还需要访问以下 URL： <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
端口 | 443（控制通道协调）<br>9443（数据传输） 
NIC 类型 | VMXNET3（如果配置服务器是 VMware VM）
 | 
要安装的软件 | 
VMware vSphere PowerCLI | 如果配置服务器在 VMware VM 上运行，则应安装 [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MYSQL | 应安装 MySQL。 可以手动安装，或者让 Site Recovery 进行安装。

配置/进程服务器大小要求

CPU | 内存 | 缓存磁盘 | 数据更改率 | 复制的计算机
--- | --- | --- | --- | ---
8 个 vCPU<br/><br/> 2 个插槽 * 4 个核心 \@ 2.5 GHz | 16GB | 300 GB | 500 GB 或更少 | < 100 台计算机
12 个 vCPU<br/><br/> 2 个插槽 * 6 个核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机
16 个 vCPU<br/><br/> 2 个插槽 * 8 个核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 台计算机

