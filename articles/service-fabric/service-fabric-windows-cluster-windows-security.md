---
title: "使用 Windows 安全性保护 Windows 上运行的群集 | Microsoft 文档"
description: "了解如何使用 Windows 安全性在 Windows 上运行的独立群集中配置节点到节点安全性和客户端到节点安全性。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>使用 Windows 安全性保护 Windows 上的独立群集
为了防止有人未经授权访问某个 Service Fabric 群集，必须保护该群集。 当群集运行生产工作负荷时，安全性就尤为重要。 本文介绍如何在 *ClusterConfig.JSON* 文件中使用 Windows 安全性配置节点到节点和客户端到节点的安全性。  该过程对应于 [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md)（创建在 Windows 上运行的独立群集）中的安全性配置步骤。 有关 Service Fabric 如何使用 Windows 安全性的详细信息，请参阅[群集安全方案](service-fabric-cluster-security.md)。

> [!NOTE]
> 由于升级群集后无法更改安全性选项，因此，应慎重地为节点到节点安全性选择适当的选项。 若要更改安全性选项，必须重建整个群集。
>
>

## <a name="configure-windows-security"></a>配置 Windows 安全性  
随 [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) 独立群集包一起下载的 *ClusterConfig.Windows.JSON* 示例配置文件包含用于配置 Windows 安全性的模板。 在“属性”部分配置 Windows 安全性：

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **配置设置** | **说明** |
| --- | --- |
| ClusterCredentialType |设置为“Windows”可启用 Windows 安全性。 |
| ServerCredentialType |设置为“Windows”可为客户端启用 Windows 安全性。<br /><br />这表示群集的客户端和群集本身在 Active Directory 域中运行。 |  
| WindowsIdentities |包含群集和客户端标识。 |  
| ClusterIdentity |使用计算机组名 domain\machinegroup 配置节点到节点安全性。 |  
| ClientIdentities |配置客户端到节点安全性。 客户端用户帐户的数组。 |  
| 标识 |为客户端标识添加域用户 domain\username。 |  
| IsAdmin |设置为 true 可指定域用户具有管理员客户端访问权限，设置为 false 可指定域用户具有用户客户端访问权限。 |  

使用 **ClusterIdentity** 配置[节点到节点安全性](service-fabric-cluster-security.md#node-to-node-security)。 若要在节点之间建立信任关系，这些节点必须能够相互识别。 为此，可创建一个域组，将所有节点包含在群集中。 该组名应在 **ClusterIdentity** 中指定。 有关详细信息，请参阅[在 Active Directory 中创建组](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)。  

使用 **ClientIdentities** 配置[客户端到节点安全性](service-fabric-cluster-security.md#client-to-node-security)。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使群集知道可以信任哪些客户端标识。 可通过两种不同的方式建立信任：

- 指定用户可以连接到的域组。
- 指定用户可以连接到的域节点。

Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种不同的访问控制类型：管理员和用户。 访问控制可让群集管理员针对不同的用户组限制某些类型的特定群集操作的访问权限，使群集更加安全。  管理员对管理功能（包括读取/写入功能）拥有完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。  

以下示例**安全性**部分配置 Windows 安全性，指定 *ServiceFabric/clusterA.contoso.com* 中的计算机位于群集中，并指定 *CONTOSO\usera* 拥有管理客户端访问权限：

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> 不应在域控制器上部署 Service Fabric。 使用计算机组或组托管服务帐户 (gMSA) 时，请确保 ClusterConfig.json 不包含域控制器的 IP 地址。
>
>

## <a name="next-steps"></a>后续步骤
在 *ClusterConfig.JSON* 文件中配置 Windows 安全性之后，请继续执行[创建 Windows 上运行的独立群集](service-fabric-cluster-creation-for-windows-server.md)中的群集创建过程。

有关节点到节点安全性、客户端到节点安全性和基于角色的访问控制的详细信息，请参阅[群集安全方案](service-fabric-cluster-security.md)。

有关使用 PowerShell 或 FabricClient 进行连接的示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。

