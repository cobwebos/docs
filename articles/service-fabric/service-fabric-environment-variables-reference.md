---
title: Azure Service Fabric 环境变量
description: 了解 Azure Service Fabric 中的环境变量。 包含变量及其用法的完整列表的引用。
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645678"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric 环境变量

Service Fabric 为每个服务实例提供了内置环境变量集。 下面是环境变量的完整列表：

| 环境变量                         | 说明                                                            | 示例                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | 应用程序的 fabric uri 名称                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | 进程所属的代码包的名称              | 代码                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | 终结点的 IP 地址或 FQDN                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | 终结点的端口号                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | 日志文件夹                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | 临时文件夹                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | 工作文件夹                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | 应用程序主文件夹                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | 一个布尔值，指定进程是否为一个容器                   | false                                                                |
| Fabric_NodeId                                | 运行进程的节点的节点 ID                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | 群集清单文件中指定的节点的 IP 或FQDN。 | localhost 或 10.0.0.1                                                |
| Fabric_NodeName                              | 运行进程的节点的节点名称                          | _Node_0                                                              |
| Fabric_ServiceName                           | 服务的结构 uri 名称（如果服务在 ExclusiveProcess 模式下托管）。 仅当使用 ServicePackageActivationMode ExclusiveProcess 创建服务时，此变量值才可用。  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | GUID                                                               |
| Fabric_ServicePackageName                    | 包含进程的服务包的名称                     | Web1Pkg                                                              |

Service Fabric 运行时使用的内部环境变量：

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
