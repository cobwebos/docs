---
title: 升级群集的 Azure Service Fabric 版本
description: 升级运行 Service Fabric 群集的 Service Fabric 代码和/或配置，包括设置群集更新模式、升级证书、添加应用程序端口、执行操作系统修补，等等。 执行升级时你会预料到哪种结果？
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 802e7402f60370b7151c5e373c8a4921a5af7c80
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789594"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>升级群集的 Service Fabric 版本

对于任何现代系统，设计可升级性都是实现产品长期成功的关键。 Azure Service Fabric 群集是你拥有的，但部分由 Microsoft 管理的资源。 本文介绍如何升级 Azure 群集中运行的 Service Fabric 版本。

可以将群集设置为 Microsoft 发布结构升级时自动接收该升级，或可以选择想要群集安装的受支持结构版本。

通过在门户设置“upgradeMode”群集配置，或者在创建实时群集时或在这之后使用 Resource Manager，来实现此目的 

> [!NOTE]
> 确保始终让群集运行受支持的结构版本。 当我们公布新版本的 Service Fabric 发布后，则标志着自该日期起至少 60 天以后结束对旧版本的支持。 新版本[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上公布。 之后新版本则可供选择。 
> 
> 

群集运行的版本到期之前的 14 天内，生成运行状况事件，将群集置于警告运行状况状态。 群集将继续处于警告状态，直至升级至支持的结构版本。

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>在 Azure 门户中设置升级模式
创建群集时可以将群集设置为自动或手动模式。

![Create_Manualmode][Create_Manualmode]

在实时群集上可以利用管理经验将群集设置为自动或手动模式。 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>在已通过门户设置为手动模式的群集上升级至新版本。
若要升级至新版本，需要执行的全部操作就是从下拉列表中选择可用版本并保存。 结构升级会被自动启动。 在升级期间，将遵守群集运行状况策略（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。

如果不符合现行的群集运行状况策略，则回滚升级。 向下滚动本文档，了解有关如何设置这些自定义运行状况策略的更多信息。 

修复造成回滚的问题后，需要按照与之前完全相同的步骤重新启动升级。

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>使用 Resource Manager 模板设置升级模式
将“upgradeMode”配置添加到 Microsoft.ServiceFabric/群集资源定义，并将“clusterCodeVersion”设置为下方列出的支持的结构版本之一，然后部署模板。 “upgradeMode”的有效值为“Manual”或“Automatic”

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>在已通过 Resource Manager 模板设置为手动模式的群集上升级至新版本。
当群集处于手动模式时，要升级到新版本，则可将“clusterCodeVersion”更改为支持的版本并部署此版本。 模板的部署启动了结构升级自动被启动。 在升级期间，将遵守群集运行状况策略（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。

如果不符合现行的群集运行状况策略，则回滚升级。  

修复造成回滚的问题后，需要按照与之前完全相同的步骤重新启动升级。

## <a name="set-custom-health-polices-for-upgrades"></a>为升级设置自定义运行状况策略
可以为结构升级指定自定义运行状况策略。 如果已将群集设置为自动结构升级，则这些策略会应用到[自动结构升级的阶段 1](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades) 中。
如果已将群集设置为手动结构升级，则在每次选择新版本时应用这些策略，来触发系统启动群集中的结构升级。 如果未重写这些策略，则会使用默认设置。

可以在“结构升级”边栏选项卡中选择高级升级设置来指定自定义运行状况策略或查看当前设置。 查看以下图片了解操作方法。 

![管理自定义运行状况策略][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>列出适用于指定订阅所有环境的所有可用版本
运行以下命令，应会获得类似于此的输出。

“supportExpiryUtc”告知指定版本的过期日期。 最新版本没有有效日期 - 它有一个值为“9999-12-31T23:59:59.9999999”，这表示尚未设置其到期日期。

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
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-in-out.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
