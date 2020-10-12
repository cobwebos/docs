---
title: 使用 Azure 策略大规模应用群集配置（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure 策略大规模应用群集配置
keywords: Kubernetes、Arc、Azure、K8s、容器
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050032"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>使用 Azure 策略大规模应用群集配置（预览版）

## <a name="overview"></a>概述

使用 Azure 策略强制实施每个 `Microsoft.Kubernetes/connectedclusters` 已启用的资源或 Git-Ops 的 `Microsoft.ContainerService/managedClusters` 资源 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 。 若要使用 Azure 策略，请选择现有策略定义并创建策略分配。 创建策略分配时，为分配设置范围：这将是 Azure 资源组或订阅。 还可以为将创建的 `sourceControlConfiguration` 设置参数。 创建分配后，策略引擎将标识位于范围内的所有 `connectedCluster` 或 `managedCluster` 资源，并将 `sourceControlConfiguration` 应用于每个资源。

如果使用多个 Git 存储库作为每个群集的可信源（例如，一个存储库用于中心 IT/群集操作员，而其他存储库用于应用程序团队），则可以通过使用多个策略分配（每个策略分配配置为使用不同的 Git 存储库）来启用此功能。

## <a name="prerequisite"></a>先决条件

请确保对 `Microsoft.Authorization/policyAssignments/write` 要在其中创建此策略分配)  (订阅或资源组的作用域具有权限。

## <a name="create-a-policy-assignment"></a>创建策略分配

1. 在 Azure 门户中，导航到“策略”，并在边栏的“创作”部分中，选择“定义”。
2. 在 "Kubernetes" 类别中选择 "部署 GitOps 到 Kubernetes 群集" 内置策略，然后单击 " **分配**"。
3. 将“范围”设置为将应用策略分配的管理组、订阅或资源组。
4. 如果要从策略范围中排除任何资源，请设置“排除”。
5. 为策略分配提供可以用于轻松识别它的“名称”和“说明”。
6. 确保“策略实施”设置为“已启用”。
7. 选择“**下一页**”。
8. 设置将在 `sourceControlConfiguration` 创建过程中使用的参数值。
9. 选择“**下一页**”。
10. 启用“创建修正任务”。
11. 确保选中“创建托管标识”，并确保标识具有“参与者”权限。 有关所需权限的详细信息，请参阅[此文档](../../governance/policy/assign-policy-portal.md)和[此文档中的注释](../../governance/policy/how-to/remediate-resources.md)。
12. 选择“查看 + 创建”。

创建策略分配之后，对于在分配范围内加载的任何新 `connectedCluster` 资源（或安装了 GitOps 代理的 `managedCluster` 资源），将应用 `sourceControlConfiguration`。 对于现有群集，需要手动运行修正任务。 策略分配通常需要 10-20 分钟才能生效。

## <a name="verify-a-policy-assignment"></a>验证策略分配

1. 在 Azure 门户中，导航到你的某个 `connectedCluster` 资源，然后在边栏的“设置”部分中，选择“策略”。  (的 UX 适用于 AKS 群集，但仍在执行。 ) 
2. 在列表中，应看到上面创建的策略分配，并且“符合性状态”应是“符合”。
3. 在边栏的“设置”部分中，选择“配置” 。
4. 在列表中，应看到策略分配创建的 `sourceControlConfiguration`。
5. 使用 kubectl 来询问群集：应看到 `sourceControlConfiguration` 创建的命名空间和项目。
6. 在 5 分钟内，你应会在群集中看到在所配置 Git 存储库的清单中描述的项目。

## <a name="next-steps"></a>后续步骤

* [使用已启用 Arc 的 Kubernetes 群集设置适用于容器的 Azure Monitor](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
