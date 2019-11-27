---
title: 手动停止或启动容器组
description: 了解如何手动停止或启动 Azure 容器实例中的容器组。
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533428"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>手动停止或启动 Azure 容器实例中的容器

容器组的 "[重新启动" 策略](container-instances-restart-policy.md)设置确定默认情况下容器实例的启动或停止方式。 您可以通过手动停止或启动容器组来覆盖默认设置。

## <a name="stop"></a>停止

手动停止正在运行的容器组，例如，使用[az container stop][az-container-stop]命令或 Azure 门户。 对于某些容器工作负荷，可能需要在定义的时间段后停止长时间运行的容器组，以节省成本。 

*容器组进入停止状态时，它会终止并回收组中的所有容器。它不保留容器状态。*

回收容器后，将解除分配[资源](container-instances-container-groups.md#resource-allocation)并为容器组停止计费。

如果容器组已终止（处于成功或失败状态），则停止操作不起作用。 例如，具有运行一次运行的容器任务的容器组成功地终止为成功状态。 尝试停止处于该状态的组不会更改状态。 

## <a name="start"></a>开始

容器组停止时-由于容器自行终止或手动停止了组，因此可以启动容器。 例如，使用[az container start][az-container-start]命令或 Azure 门户手动启动组中的容器。 如果更新了任何容器的容器映像，则会拉取一个新映像。 

启动容器组会使用相同的容器配置开始一个新部署。 此操作可帮助你快速重复使用按预期方式工作的已知容器组配置。 你无需创建新的容器组便可运行相同的工作负荷。

容器组中的所有容器都是通过此操作启动的。 无法在组中启动特定容器。

手动启动或重启容器组后，容器组将根据所配置的重启策略运行。
  
## <a name="restart"></a>重新启动

可以在容器组运行时重新启动它，例如，使用[az container restart][az-container-restart]命令。 此操作会重启容器组中的所有容器。 如果更新了任何容器的容器映像，则会拉取一个新映像。 

如果你想要排查部署问题，则重启容器组会有所帮助。 例如，如果临时资源限制阻止了你的容器成功运行，则重启组可能会解决此问题。

此操作将重启容器组中的所有容器。 无法重新启动组中的特定容器。

手动重启容器组后，容器组会根据配置的重新启动策略运行。

## <a name="next-steps"></a>后续步骤

详细了解如何[重新启动](container-instances-restart-policy.md)Azure 容器实例中的策略设置。

除了使用现有配置手动停止和启动容器组外，还可以更新正在运行的容器组的[设置](container-instances-update.md)。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
