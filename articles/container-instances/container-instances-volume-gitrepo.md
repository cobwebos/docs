---
title: 在 Azure 容器实例中装载 gitRepo 卷
description: 了解如何在容器实例中装载 gitRepo 卷以克隆 Git 存储库
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325441"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载 gitRepo 卷

了解如何在容器实例中装载 *gitRepo* 卷以克隆 Git 存储库。

> [!NOTE]
> 当前只有 Linux 容器能装载 *gitRepo* 卷。 尽管我们正在努力将所有功能带入 Windows 容器, 但你可以在 "[概述](container-instances-overview.md#linux-and-windows-containers)" 中找到当前的平台差异。

## <a name="gitrepo-volume"></a>gitRepo 卷

*gitRepo* 卷可在容器启动时装载目录，并将指定的 Git 存储库克隆到该目录。 在容器实例中使用 *gitRepo* 卷，可避免在应用程序中为执行此操作添加代码。

装载 *gitRepo* 卷时，可以设置三个属性以对卷进行配置：

| 属性 | 必填 | 描述 |
| -------- | -------- | ----------- |
| `repository` | 是 | 要克隆的 Git 存储库的完整 URL，包括 `http://` 或 `https://`。|
| `directory` | 否 | 存储库应克隆到的目录。 路径不得包含“`..`”，也不能以其开头。  如果指定“`.`”，存储库将克隆到卷的目录。 否则，Git 存储库将克隆到卷目录中给定名称的子目录。 |
| `revision` | 否 | 要克隆的修订的提交哈希。 如果未指定，则克隆 `HEAD` 修订。 |

## <a name="mount-gitrepo-volume-azure-cli"></a>装载 gitRepo 卷:Azure CLI

若要在使用[Azure CLI](/cli/azure)部署容器实例时装载 gitRepo 卷, 请向`--gitrepo-url` [az container create][az-container-create]命令提供和`--gitrepo-mount-path`参数。 还可以指定要将卷克隆到其中的目录 (`--gitrepo-dir`) 和要克隆的修订版的提交哈希 (`--gitrepo-revision`)。

此示例命令将 Microsoft [helloworld][aci-helloworld]示例应用程序克隆到`/mnt/aci-helloworld`容器实例中:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

若要验证是否已装载 gitRepo 卷, 请使用[az container exec][az-container-exec]在容器中启动 shell, 并列出该目录:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>装载 gitRepo 卷:资源管理器

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

## <a name="private-git-repo-authentication"></a>专用 Git 存储库身份验证

若要为专用 Git 存储库装载 gitRepo 卷，请在存储库 URL 中指定凭据。 通常，凭据采用用户名和个人访问令牌 (PAT) 的形式，授予对存储库的范围访问权限。

例如，专用 GitHub 存储库的 Azure CLI `--gitrepo-url` 参数将类似于以下内容（其中“gituser”是 GitHub 用户名，“abcdef1234fdsa4321abcdef”是用户的个人访问令牌）：

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

对于 Azure Repos Git 存储库，请指定任何用户名（可以使用“azurereposuser”，如下例所示）并结合有效的 PAT：

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

有关 GitHub 和 Azure Repos 的个人访问令牌的详细信息，请参阅以下内容：

GitHub[为命令行创建个人访问令牌][pat-github]

Azure Repos：[创建个人访问令牌以对访问进行身份验证][pat-repos]

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
