---
title: 在 Azure 容器实例中装载 gitRepo 卷
description: 了解如何在容器实例中装载 gitRepo 卷以克隆 Git 存储库
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
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

## <a name="mount-gitrepo-volume-azure-cli"></a>装载 gitRepo 卷：Azure CLI

在使用 [Azure CLI](/cli/azure) 部署容器实例时若要装载 gitRepo 卷，请在 [az container create][az-container-create] 命令中提供 `--gitrepo-url` 和 `--gitrepo-mount-path` 参数。 还可以指定要将卷克隆到其中的目录 (`--gitrepo-dir`) 和要克隆的修订版的提交哈希 (`--gitrepo-revision`)。

此示例命令将 [aci-helloworld][aci-helloworld] 示例应用程序克隆到容器实例中的 `/mnt/aci-helloworld`：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

若要验证 gitRepo 卷是否已装载，请使用 [az container exec][az-container-exec] 在该容器中启动 shell 并列出目录：

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>装载 gitRepo 卷：资源管理器

在使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)部署容器实例时若要装载 gitRepo 卷，请首先填充模板的容器组 `properties` 节中的 `volumes` 数组。 然后，针对容器组中希望装载 *gitRepo* 卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

例如，以下资源管理器模板创建了一个包含单个容器的容器组。 该容器克隆由 *gitRepo* 卷块指定的两个 GitHub 存储库。 第二个卷包括其他属性以指定要克隆到的目录和要克隆的特定修订的提交哈希。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
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

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec