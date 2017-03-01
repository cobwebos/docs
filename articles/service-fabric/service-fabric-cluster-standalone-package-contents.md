---
title: "适用于 Windows Server 的 Service Fabric 独立包 | Microsoft Docs"
description: "适用于 Windows Server 的 Azure Service Fabric 独立包的说明和内容。"
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/15/2017
ms.author: chackdan;maburlik
translationtype: Human Translation
ms.sourcegitcommit: 523a26ad37ab45fabcc5fc99c291abf864297a22
ms.openlocfilehash: 88a4b9832fa2c0bf0f22e65dec1787e137398791
ms.lasthandoff: 02/16/2017


---

# <a name="package-contents-of-service-fabric-standalone-package-for-windows-server"></a>适用于 Windows Server 的 Service Fabric 独立包的包内容
在[下载的](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric 独立包中，可找到以下文件：

| **文件名** | **简短说明** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |PowerShell 脚本，用于通过 ClusterConfig.json 中的设置创建群集。 |
| RemoveServiceFabricCluster.ps1 |PowerShell 脚本，用于通过 ClusterConfig.json 中的设置删除群集。 |
| AddNode.ps1 |PowerShell 脚本，用于在当前计算机上将节点添加到现有的部署群集。 |
| RemoveNode.ps1 |PowerShell 脚本，用于在当前计算机上将节点从现有的部署群集中删除。 |
| CleanFabric.ps1 |PowerShell 脚本，用于从当前计算机中清除独立 Service Fabric 安装。 应使用以前的 MSI 安装的自身关联卸载程序来删除以前的安装。 |
| TestConfiguration.ps1 |PowerShell 脚本，用于分析 Cluster.json 中指定的基础结构。 |
| DownloadServiceFabricRuntimePackage.ps1 |用于下载最新带外运行时包的 PowerShell 脚本，适用于部署计算机未连接到 Internet 的方案。 |
| DeploymentComponentsAutoextractor.exe |包含独立包脚本所用部署组件的自解压缩存档。 |
| EULA_ENU.txt |有关使用 Microsoft Azure Service Fabric Windows Server 独立包的许可条款。 现在，可以[下载 EULA 的副本](http://go.microsoft.com/fwlink/?LinkID=733084)。 |
| Readme.txt |发行说明和基本安装说明的链接。 这是本文中说明的子集。 |
| ThirdPartyNotice.rtf |包中的第三方软件的通知。 |

**模板** 
| **文件名** | **简短说明** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |群集配置示例文件，其中包含非安全型三节点式单计算机（或虚拟机）开发群集的设置，这些设置包括群集中每个节点的信息。 |
| ClusterConfig.Unsecure.MultiMachine.json |群集配置示例文件，其中包含非安全型多计算机（或虚拟机）群集的设置，这些设置包括群集中每个计算机的信息。 |
| ClusterConfig.Windows.DevCluster.json |群集配置示例文件，其中包含安全型三节点式单计算机（或虚拟机）开发群集的所有设置，这些设置包括群集中每个节点的信息。 此群集使用 [Windows 标识](https://msdn.microsoft.com/library/ff649396.aspx)进行保护。 |
| ClusterConfig.Windows.MultiMachine.json |群集配置示例文件，其中包含使用 Windows 安全性措施的安全型多计算机（或虚拟机）群集的所有设置，这些设置包括安全群集中每个计算机的信息。 此群集使用 [Windows 标识](https://msdn.microsoft.com/library/ff649396.aspx)进行保护。 |
| ClusterConfig.x509.DevCluster.json |群集配置示例文件，其中包含安全型三节点式单计算机（或虚拟机）开发群集的所有设置，这些设置包括群集中每个节点的信息。 此群集使用 x509 证书进行保护。 |
| ClusterConfig.x509.MultiMachine.json |群集配置示例文件，其中包含安全型多计算机（或虚拟机）群集的所有设置，这些设置包括安全群集中每个节点的信息。 此群集使用 x509 证书进行保护。 |
| ClusterConfig.gMSA.Windows.MultiMachine.json |群集配置示例文件，其中包含安全型多计算机（或虚拟机）群集的所有设置，这些设置包括安全群集中每个节点的信息。 使用[组托管服务帐户](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx)保护该群集。 |

# <a name="cluster-configuration-samples"></a>群集配置示例
可在以下 Github 页面找到最新版本的群集配置模板：[独立群集配置示例](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)。

## <a name="related"></a>相关内容
* [创建独立 Azure Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric 群集安全性方案](service-fabric-windows-cluster-windows-security.md)

