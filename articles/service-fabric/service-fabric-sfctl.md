---
title: Azure Service Fabric CLI- sfctl | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 23adc7fa6ee53d1c5edd10b2772f8825dbcef2a8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl"></a>sfctl 
用于管理 Service Fabric 群集和实体的命令。 此版本与 Service Fabric 6.0 运行时兼容。 命令遵循名词谓词模式，有关详细信息，请参阅下列子组。

## <a name="subgroups"></a>子组
|子组|说明|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| 创建、删除和管理应用程序及应用程序类型。|
| [chaos](service-fabric-sfctl-chaos.md)   | 启动、停止和报告混沌测试服务。|
| [cluster](service-fabric-sfctl-cluster.md) | 选择、管理和运行 Service Fabric 群集。|
| [compose](service-fabric-sfctl-compose.md) | 创建、删除和管理 Docker Compose 应用程序。|
| [is](service-fabric-sfctl-is.md)      | 查询并向基础结构服务发送命令。|
| [node](service-fabric-sfctl-node.md)    | 管理构成群集的节点。|
| [partition](service-fabric-sfctl-partition.md)  | 查询和管理任何服务的分区。|
| [rpm](service-fabric-sfctl-rpm.md)        | 查询并向修复管理器服务发送命令。|
| [replica](service-fabric-sfctl-replica.md) | 管理属于服务分区的副本。|
| [service](service-fabric-sfctl-service.md) | 创建、删除和管理服务、服务类型和服务包。|
| [store](service-fabric-sfctl-store.md)   | 针对群集映像存储执行基本文件级别操作。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。