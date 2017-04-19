---
title: "Microsoft Azure Stack 概念证明 (POC) 体系结构 | Microsoft Docs"
description: "查看 Microsoft Azure Stack POC 体系结构。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: helaw
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 6c7fbd401a04ea85e391eeeb578c20eb7a5ff2ea
ms.lasthandoff: 03/31/2017


---
# <a name="microsoft-azure-stack-poc-architecture"></a>Microsoft Azure Stack POC 体系结构
Azure Stack POC 是 Azure Stack Technical Preview 3 的单节点部署。 所有组件安装在单主机计算机上运行的虚拟机中。 

## <a name="logical-architecture-diagram"></a>逻辑体系结构示意图
下图演示了 Azure Stack POC 及其组件的逻辑体系结构。

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>虚拟机角色
Azure Stack POC 提供在 POC 主机上使用以下 VM 的服务：

| Name | 说明 |
| ----- | ----- |
| **MAS-ACS01** | Azure Stack 存储服务。|
| **MAS-ADFS01** | Active Directory 联合身份验证服务 (ADFS)。  |
| **MAS-BGPNAT01** | 边缘路由器，为 Azure Stack 提供 NAT 和 VPN 功能。 |
| **MAS-CA01** | Azure Stack 角色服务的证书颁发机构服务。|
| **MAS-CON01** | 用于安装 PowerShell、Visual Studio 和其他工具的控制台计算机。|
| **MAS-DC01** | Microsoft Azure Stack 的 Active Directory、DNS 和 DHCP 服务。|
| **MAS-ERCS01** | 紧急恢复控制台 VM。 |
| **MAS-GWY01** | 边缘网关服务，例如租户网络的 VPN 站点到站点连接。|
| **MAS-NC01** | 用于管理 Azure Stack 网络服务的网络控制器。  |
| **MAS-SLB01** | Azure Stack 中用于租户和 Azure Stack 基础结构服务的负载均衡多路复用器服务。  |
| **MAS-SUS01** | Windows Server 更新服务，负责为其他 Azure Stack 虚拟机提供更新。|
| **MAS-SQL01** | Azure Stack 基础结构角色的内部数据存储。  |
| **MAS-WAS01** | Azure Stack 管理门户和 Azure Resource Manager 服务。|
| **MAS-WASP01**| Azure Stack 用户（租户）门户和 Azure Resource Manager 服务。|
| **MAS-XRP01** | Microsoft Azure Stack 的基础结构管理控制器，包括计算、网络和存储资源提供程序。|

## <a name="storage-services"></a>存储服务
**虚拟磁盘**、**存储空间**和**存储空间直通**是 Windows Server 中用于启用 Microsoft Azure Stack 存储资源提供程序的相应基础存储技术。  安装在物理主机上的操作系统中的其他存储服务包括：

| 名称 | 说明 |
| ----- | ----- |
| **ACS Blob 服务** | Azure 一致的存储 Blob 服务，提供 Blob 和表存储服务。 |
| **SoFS** | 横向扩展文件服务器。|
| **ReFS CSV** |弹性文件系统群集共享卷。|


## <a name="next-steps"></a>后续步骤
[部署 Azure Stack](azure-stack-deploy.md)

[尝试的第一个方案](azure-stack-first-scenarios.md)


