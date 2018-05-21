---
title: 在 Azure Service Fabric 中管理多个环境的应用程序 | Microsoft Docs
description: Azure Service Fabric 应用程序可以在规模为一台计算机到数千台计算机的群集上运行。 在某些情况下，需要以不同的方式针对各种环境配置应用程序。 本文介绍如何为每个环境定义不同的应用程序参数。
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 15ad606578970290cef440ec4efdd967ca0c0b32
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="manage-applications-for-multiple-environments"></a>管理多个环境的应用程序

借助 Azure Service Fabric 群集，可以在任何位置，使用任意数量的计算机（从一台到数千台）来创建群集。 大多数情况下，必须跨多个群集配置（本地开发群集、共享开发群集和生产群集）来部署应用程序。 所有这些群集被视为运行代码的不同环境。 尽管无需修改，应用程序二进制文件也可在这些不同环境中运行，但用户通常希望以不同方式配置应用程序。

请参见以下两个简单的示例：
  - 你的服务在已定义的端口上侦听，但是你需要该端口在各个环境中有所不同
  - 你需要在各个环境中为数据库提供不同的绑定凭据

## <a name="specifying-configuration"></a>指定配置

提供的配置可分为两个类别：

- 应用于服务运行方式的配置
  - 例如，终结点的端口号或服务的实例数量
  - 此配置是在应用程序或服务清单文件中指定的
- 应用于应用程序代码的配置
  - 例如，绑定数据库的信息
  - 此配置可通过配置文件或环境变量来提供

> [!NOTE]
> 并非应用程序和服务清单文件中的所有属性都支持参数。
> 在这些情况下，用户必须依赖于将字符串替换为部署工作流的一部分。 在 Visual Studio Team Services 中，可以使用类似于替换令牌：https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens 的扩展，或者在 Jenkins 中通过运行脚本任务来替换值。
>

## <a name="specifying-parameters-during-application-creation"></a>在应用程序创建期间指定参数

在 Service Fabric 中创建命名的应用程序实例时，可以选择传入参数。 执行此操作的方式取决于创建应用程序实例的方式。

  - 在 PowerShell 中，[`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 将应用程序参数作为哈希表。
  - 借助 sfctl，[`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) 命令将参数作为 JSON 字符串。 Install.sh 脚本使用 sfctl。
  - Visual Studio 在应用程序项目的参数文件夹中提供一组参数文件。 从 Visual Studio 发布、使用 Visual Studio Team Service 或 Team Foundation Server 时会用到这些参数文件。 在 Visual Studio 中，参数文件会传递给 Deploy-FabricApplication.ps1 脚本。

## <a name="next-steps"></a>后续步骤
以下文章演示如何使用此处所述的某些概念：

- [如何在 Service Fabric 中指定服务的环境变量](service-fabric-how-to-specify-environment-variables.md)
- [如何在 Service Fabric 中使用参数来指定服务的端口号](service-fabric-how-to-specify-port-number-using-parameters.md)
- [如何参数化配置文件](service-fabric-how-to-parameterize-configuration-files.md)

- [环境变量引用](service-fabric-environment-variables-reference.md)
