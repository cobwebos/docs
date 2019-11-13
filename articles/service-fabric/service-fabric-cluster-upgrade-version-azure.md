---
title: 升级群集的 Azure Service Fabric 版本
description: 升级运行 Service Fabric 群集的 Service Fabric 代码和/或配置，包括设置群集更新模式、升级证书、添加应用程序端口、执行操作系统修补，等等。 执行升级时可以预期什么？
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 067bf654928240ffe9651d4acf1933a81e631146
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013341"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>升级群集的 Service Fabric 版本

对于任何新式系统而言，为可升级性做好规划是实现产品长期成功的关键所在。 Azure Service Fabric 群集是你拥有的，但部分由 Microsoft 管理的资源。 本文介绍如何升级 Azure 群集中运行的 Service Fabric 版本。

可以将群集设置为 Microsoft 发布结构升级时自动接收该升级，或可以选择想要群集安装的受支持结构版本。

为此，请门户上设置“upgradeMode”群集配置，或者在创建时或稍后在实时群集上使用 Resource Manager 进行设置。 

> [!NOTE]
> 请确保群集始终运行受支持的结构版本。 当我们宣布发行新版 Service Fabric 时，以前的版本标记为自发布日期开始算起的 60 天后结束支持。 新版发布会在 [Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)中通告。 之后新版本则可供选择。 
> 
> 

群集运行的版本过期前 14 天，系统会生成运行状况事件，使群集进入警告运行状况状态。 在升级到支持的结构版本之前，群集将保持警告状态。

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>在 Azure 门户中设置升级模式
创建群集时可以将群集设置为自动或手动模式。

![Create_Manualmode][Create_Manualmode]

在实时群集上可以利用管理经验将群集设置为自动或手动模式。 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>在设置为手动模式的群集上，通过门户升级到新版本。
若要升级到新版本，只需从下拉列表中选择可用的版本并保存即可。 结构升级自动开始。 升级过程中遵守群集运行状况策略（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。

如果不符合现行的群集运行状况策略，则回滚升级。 请在本文档中向下滚动，详细了解如何设置这些自定义运行状况策略。 

解决导致回滚的问题后，需要遵循前面相同的步骤再次启动升级。

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>使用 Resource Manager 模板设置升级模式
将“upgradeMode”配置添加到 Microsoft.ServiceFabric/clusters 资源定义，将“clusterCodeVersion”设置为支持的结构版本之一，如下所示，并部署模板。 “upgradeMode”的有效值为“Manual”或“Automatic”。

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>在设置为手动模式的群集上，通过 Resource Manager 模板升级到新版本。
当群集处于手动模式时，要升级到新版本，请将“clusterCodeVersion”更改为支持的版本，然后部署该版本。 部署模板时，自动开始结构升级。 升级过程中遵守群集运行状况策略（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。

如果不符合现行的群集运行状况策略，则回滚升级。  

解决导致回滚的问题后，需要遵循前面相同的步骤再次启动升级。

## <a name="set-custom-health-polices-for-upgrades"></a>为升级设置自定义运行状况策略
可为结构升级指定自定义运行状况策略。 如果已将群集设置为自动结构升级，则这些策略会应用到[自动结构升级的阶段 1](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades) 中。
如果已将群集设置为“手动”结构升级，则每当选择新版本，因而触发系统开始在群集中进行结构升级时，就会应用这些策略。 如果未重写这些策略，则使用默认值。

在“结构升级”边栏选项卡下面，可以选择高级升级设置来指定自定义运行状况策略，或者查看当前设置。 请参考下图了解操作方法。 

![管理自定义运行状况策略][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>列出适用于指定订阅所有环境的所有可用版本
运行以下命令，应会看到类似于下面的输出。

“supportExpiryUtc”告知给定的版本何时即将到期或已过期。 最新版本没有有效日期 - 它的值为“9999-12-31T23:59:59.9999999”，这只是表示尚未设置过期日期。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>后续步骤
* 了解如何自定义 [Service Fabric 群集结构设置](service-fabric-cluster-fabric-settings.md)的部分内容
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-up-down.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
