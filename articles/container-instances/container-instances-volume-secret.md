---
title: 在 Azure 容器实例中装载机密卷
description: 了解如何装载机密卷以存储供容器实例访问的敏感信息
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: e20dc3b37bc766b2a8a352eb62c03f5e2ee14c52
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载机密卷

了解如何在存储的容器实例中装载*机密*卷，以及容器组中的容器如何检索敏感信息。

> [!NOTE]
> 当前只有 Linux 容器能装载*机密*卷。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="secret-volume"></a>机密卷

可以使用*机密*卷向容器组中的容器提供敏感信息。 *机密*卷将指定的机密存储在该卷内的文件中，然后容器组中的容器可以访问这些机密。 使用*机密*卷中的机密，可以避免将敏感数据（例如，SSH 密钥或数据库凭据）置于应用程序代码中。

所有*机密*卷由 [tmpfs][tmpfs] 提供支持，后者是一个支持 RAM 的文件系统，其内容永远不会写入到非易失性存储。

## <a name="mount-a-secret-volume"></a>装载机密卷

若要将*机密*卷装载到容器实例中，必须使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)进行部署。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 接下来，针对容器组中希望装载*机密*卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

例如，以下资源管理器模板创建了一个包含单个容器的容器组。 该容器装载了一个包含两个 Base64 编码机密的*机密*卷。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

若要查看使用 Azure 资源管理器模板进行的容器实例部署示例，请参阅[在 Azure 容器实例中部署多容器组](container-instances-multi-container-group.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs