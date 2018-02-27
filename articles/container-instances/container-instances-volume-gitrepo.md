---
title: "在 Azure 容器实例中装载 gitRepo 卷"
description: "了解如何在容器实例中装载 gitRepo 卷以克隆 Git 存储库"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载 gitRepo 卷

了解如何在容器实例中装载 *gitRepo* 卷以克隆 Git 存储库。

> [!NOTE]
> 当前只有 Linux 容器能装载 *gitRepo* 卷。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="gitrepo-volume"></a>gitRepo 卷

*gitRepo* 卷可在容器启动时装载目录，并将指定的 Git 存储库克隆到该目录。 在容器实例中使用 *gitRepo* 卷，可避免在应用程序中为执行此操作添加代码。

装载 *gitRepo* 卷时，可以设置三个属性以对卷进行配置：

| 属性 | 必选 | 说明 |
| -------- | -------- | ----------- |
| `repository` | 是 | 要克隆的 Git 存储库的完整 URL，包括 `http://` 或 `https://`。|
| `directory` | 否 | 存储库应克隆到的目录。 路径不得包含“`..`”，也不能以其开头。  如果指定“`.`”，存储库将克隆到卷的目录。 否则，Git 存储库将克隆到卷目录中给定名称的子目录。 |
| `revision` | 否 | 要克隆的修订的提交哈希。 如果未指定，则克隆 `HEAD` 修订。 |

## <a name="mount-a-gitrepo-volume"></a>装载 gitRepo 卷

若要将 *gitRepo* 卷装载到容器实例中，必须使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)进行部署。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 接下来，针对容器组中希望装载 *gitRepo* 卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

例如，以下资源管理器模板创建了一个包含单个容器的容器组。 该容器克隆由 *gitRepo* 卷块指定的两个 GitHub 存储库。 第二个卷包括其他属性以指定要克隆到的目录和要克隆的特定修订的提交哈希。

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

前面的模板中定义的两个克隆存储库的生成目录结构如下：

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

若要查看使用 Azure 资源管理器模板进行的容器实例部署示例，请参阅[在 Azure 容器实例中部署多容器组](container-instances-multi-container-group.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)
