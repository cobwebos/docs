---
title: 升级独立群集的配置
description: 了解如何升级运行独立 Service Fabric 群集的配置。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8d0279cc323f7eee87feb2a596a4c2df0b4667e1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790841"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>升级独立群集的配置 

对于任何新式系统而言，升级能力是实现产品长期成功的关键所在。 Azure Service Fabric 群集是你拥有的资源。 本文介绍如何升级独立 Service Fabric 群集的配置设置。

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>在 ClusterConfig.json 文件中自定义群集设置
通过“ClusterConfig.json”文件配置独立群集  。 若要了解不同设置的详细信息，请参阅[独立 Windows 群集的配置设置](service-fabric-cluster-manifest.md)。

可以在“ClusterConfig.json”中[群集属性](./service-fabric-cluster-manifest.md#cluster-properties)部分下的 `fabricSettings` 部分中添加、更新或删除设置  。 

例如，以下 JSON 将向 `fabricSettings` 下的“诊断”部分添加新设置“MaxDiskQuotaInMB”   ：

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

修改 ClusterConfig.json 文件中的设置后，[测试群集配置](#test-the-cluster-configuration)，再[升级群集配置](#upgrade-the-cluster-configuration)，将设置应用到群集。 

## <a name="test-the-cluster-configuration"></a>测试群集配置
启动配置升级前，可通过运行独立程序包中的以下 PowerShell 脚本来测试新群集配置 JSON：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

或使用此脚本：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

某些配置不能升级（如终结点、群集名称、节点 IP 等）。将会针对旧配置测试新的群集配置 JSON ，并在出现问题时在 PowerShell 窗口中引发错误。

## <a name="upgrade-the-cluster-configuration"></a>升级群集配置
若要升级群集配置，请运行 [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)。 按升级域逐个处理配置升级。

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>升级群集证书配置
群集证书用于群集节点之间的身份验证。 执行证书滚动更新时格外小心，因为发生失败会导致群集节点之间无法通信。

支持四个选项：  

* 单证书升级：升级路径为“证书 A（主证书）-> 证书 B（主证书）-> 证书 C（主证书）->....”

* 双证书升级：升级路径为“证书 A（主证书）-> 证书 A（主证书）和证书 B（辅助证书）-> 证书 B（主证书）-> 证书 B（主证书）和证书 C（辅助证书）-> 证书 C（主证书）-> ...”

* 证书类型升级：基于指纹的证书配置 <-> 基于 CommonName 的证书配置。 例如，“证书指纹 A（主证书）和指纹 B（辅助证书）-> 证书 CommonName C”。

* 证书颁发者指纹升级：升级路径为“Certificate CN=A,IssuerThumbprint=IT1 (Primary) -> Certificate CN=A,IssuerThumbprint=IT1,IT2 (Primary) -> Certificate CN=A,IssuerThumbprint=IT2 (Primary)”。


## <a name="next-steps"></a>后续步骤
* 了解如何自定义某些 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)。
* 了解如何[扩大和缩小群集](service-fabric-cluster-scale-in-out.md)。
* 了解[应用程序升级](service-fabric-application-upgrade.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
