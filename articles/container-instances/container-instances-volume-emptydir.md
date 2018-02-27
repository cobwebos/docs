---
title: "在 Azure 容器实例中装载 emptyDir 卷"
description: "了解如何在 Azure 容器实例中装载 emptyDir 卷以在容器组中的容器之间共享数据"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 01b44f357d160a48852ee843cd8bebd138e95dfc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载 emptyDir 卷

了解如何在 Azure 容器实例中装载 *emptyDir* 卷以在容器组中的容器之间共享数据。

> [!NOTE]
> 当前只有 Linux 容器能装载 *emptyDir* 卷。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="emptydir-volume"></a>emptyDir 卷

*emptyDir* 卷提供了容器组中的每个容器都可访问的可写目录。 该组中的容器可以读取和写入此卷中的相同文件，并且可以在每个容器中使用相同或不同路径装载此卷。

一些示例用于 *emptyDir* 卷：

* 暂存空间
* 长时间运行任务期间的检查点
* 存储由挎斗容器检索的数据以及由应用程序容器提供的数据

*emptyDir* 卷中的数据将一直保留到容器崩溃。 但是，并不保证重新启动的容器能够持久保留 *emptyDir* 卷中的数据。

## <a name="mount-an-emptydir-volume"></a>装载 emptyDir 卷

若要将 emptyDir 卷装载到容器实例中，必须使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)进行部署。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 接下来，针对容器组中希望装载 *emptyDir* 卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

例如，以下资源管理器模板创建了一个包含两个容器的容器组，每个容器均装载了 *emptyDir* 卷：

[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

若要查看使用 Azure 资源管理器模板进行的容器实例部署示例，请参阅[在 Azure 容器实例中部署多容器组](container-instances-multi-container-group.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)