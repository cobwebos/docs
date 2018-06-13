---
title: Azure 堆栈开发工具包体系结构 |Microsoft 文档
description: 介绍 Azure 堆栈开发工具包 (ASDK) 体系结构。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
ms.locfileid: "29976409"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure 堆栈开发工具包体系结构
Azure 堆栈开发工具包 (ASDK) 是单节点部署的 Azure 堆栈。 所有组件安装在单主机计算机上运行的虚拟机中。 

## <a name="logical-architecture-diagram"></a>逻辑体系结构示意图
下图说明了 ASDK 及其组件的逻辑体系结构。

![ASDK 体系结构](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>虚拟机角色
ASDK 提供服务使用以下开发工具包主机计算机上承载的 Vm:

| 名称 | 说明 |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack 存储服务。|
| **AzS-ADFS01** | Active Directory 联合身份验证服务 (ADFS)。  |
| **AzS-BGPNAT01** | 边缘路由器，为 Azure Stack 提供 NAT 和 VPN 功能。 |
| **AzS-CA01** | Azure Stack 角色服务的证书颁发机构服务。|
| **AzS-DC01** | Microsoft Azure Stack 的 Active Directory、DNS 和 DHCP 服务。|
| **AzS-ERCS01** | 紧急恢复控制台 VM。 |
| **AzS-GWY01** | 边缘网关服务，例如租户网络的 VPN 站点到站点连接。|
| **AzS-NC01** | 用于管理 Azure Stack 网络服务的网络控制器。  |
| **AzS-SLB01** | Azure Stack 中用于租户和 Azure Stack 基础结构服务的负载均衡多路复用器服务。  |
| **AzS-SQL01** | Azure Stack 基础结构角色的内部数据存储。  |
| **AzS-WAS01** | Azure Stack 管理门户和 Azure 资源管理器服务。|
| **AzS-WASP01**| Azure Stack 用户（租户）门户和 Azure 资源管理器服务。|
| **AzS-XRP01** | Microsoft Azure Stack 的基础结构管理控制器，包括计算、网络和存储资源提供程序。|


## <a name="next-steps"></a>后续步骤
[了解基本 ASDK 管理任务](asdk-admin-basics.md)
