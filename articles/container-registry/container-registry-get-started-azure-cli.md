---
title: 快速入门 - 创建注册表 - Azure CLI
description: 快速了解如何使用 Azure CLI 创建专用 Docker 容器注册表。
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 551a3659feb39943c9f794484abb6f2da4367f39
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74455169"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建专用容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 Azure CLI 创建 Azure 容器注册表实例。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

本快速入门需要运行 Azure CLI（建议运行 2.0.55 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

由于 Azure Cloud Shell 并不包括所有必需的 Docker 组件（`dockerd` 守护程序），因此不能将 Cloud Shell 用于本快速入门。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create][az-group-create]”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>创建容器注册表

本快速入门将创建一个“基本”注册表。该注册表已针对成本进行优化，是可供开发人员了解 Azure 容器注册表的选项。  有关可用服务层级的详细信息，请参阅[容器注册表 SKU][container-registry-skus]。

使用 [az acr create][az-acr-create] 命令创建 ACR 实例。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例使用 myContainerRegistry007  。 将其更新为唯一值。

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

创建注册表时，输出与下面类似：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

记下输出中的 `loginServer`，这是完全限定的注册表名称（全部小写）。 在本快速入门的剩余部分，`<acrName>` 是容器注册表名称的占位符。

## <a name="log-in-to-registry"></a>登录到注册表

在推送和拉取容器映像之前，必须登录到注册表。 为此，请使用 [az acr login][az-acr-login] 命令。

```azurecli
az acr login --name <acrName>
```

该命令在完成后会返回消息 `Login Succeeded`。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

以下示例列出了注册表中的存储库：

```azurecli
az acr repository list --name <acrName> --output table
```

输出：

```
Result
----------------
hello-world
```

以下示例列出了 hello-world 存储库中的标记  。

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

输出：

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

如果不再需要存储在该处的资源组、容器注册表和容器映像，可以使用 [az group delete][az-group-delete] 命令将其删除。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure CLI 创建 Azure 容器注册表，如何将容器映像推送到注册表，以及如何拉取并运行注册表中的映像。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
