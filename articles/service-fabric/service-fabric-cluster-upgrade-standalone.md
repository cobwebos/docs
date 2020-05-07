---
title: 升级 Azure Service Fabric 独立群集
description: 了解有关升级 Azure Service Fabric 独立群集的版本或配置的信息。  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: ac3e80aae440c6709057e4211f7a4f6a1beea058
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790637"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>升级和更新 Service Fabric 独立群集

对于任何现代系统，设计可升级性都是实现产品长期成功的关键。 Azure Service Fabric 独立群集是你拥有的资源。 本文介绍可以升级或更新的内容。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制在群集上运行的结构版本
请确保群集始终运行[受支持的 Service Fabric 版本](service-fabric-versions.md)。 当 Microsoft 宣布推出新版 Service Fabric 时，即标志着自宣布日期起至少 60 天后，将结束对旧版的支持。 新版本将[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)中宣布。 从该时间开始，便可以选择使用新版本。

可以将群集设置为 Microsoft 发布结构升级时自动接收该升级，也可以手动选择希望群集安装的受支持结构版本。 有关详细信息，请阅读[升级群集上运行的 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)。

## <a name="customize-configuration-settings"></a>自定义配置设置

可以在 ClusterConfig.json 文件中设置许多不同的[配置设置](service-fabric-cluster-manifest.md)，例如群集的可靠性级别和节点属性  。  若要了解详细信息，请阅读[升级独立群集的配置](service-fabric-cluster-config-upgrade-windows-server.md)。  还可自定义许多其他更高级的设置。  有关详细信息，请参阅 [Service Fabric 群集结构设置](service-fabric-cluster-fabric-settings.md)。

## <a name="define-node-properties"></a>定义节点属性
有时，可能需要确保仅在群集中特定类型的节点上运行某些工作负荷。 例如，某些工作负荷可能需要 GPU 或 SSD，而有些则不用。 对于群集中的每个节点类型，可以向群集节点添加自定义节点属性。 放置约束是附加到单个服务的语句，这些服务专供 1 个或多个节点属性选择。 放置约束定义服务运行的位置。

有关使用放置约束、节点属性以及如何定义它们的详细信息，请参阅[节点属性和放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。
 

## <a name="add-capacity-metrics"></a>添加容量指标
对于每个节点类型，可以添加要在应用程序中用于报告负载的自定义容量度量值。 有关使用容量指标来报告负载的详细信息，请参阅 Service Fabric 群集 Resource Manager 文档[描述群集](service-fabric-cluster-resource-manager-cluster-description.md)，以及[指标和负载](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="patch-the-os-in-the-cluster-nodes"></a>修补群集节点的操作系统
修补业务流程应用程序 (POA) 是一个 Service Fabric 应用程序，可在 Service Fabric 群集中自动修补操作系统，而无需停机。 [适用于 Windows 的修补业务流程应用程序](service-fabric-patch-orchestration-application.md)可部署在群集上，以便以协调一致的方式安装修补程序，同时使服务始终可用。 


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
