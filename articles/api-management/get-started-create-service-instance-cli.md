---
title: 快速入门 - 使用 CLI 创建 Azure API 管理实例（预览版）
description: 使用 Azure CLI 创建新的 Azure API 管理服务实例。
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 8e41e20752e4203bf9916813b86cd901f4f75c09
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708217"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>快速入门：使用 Azure CLI 创建新的 Azure API 管理服务实例（预览版）

Azure API 管理 (APIM) 可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 使用 APIM 可以为在任何位置托管的现有后端服务创建和管理新式 API 网关。 有关详细信息，请参阅[概述](api-management-key-concepts.md)。

本快速入门介绍了在 Azure CLI 中使用 [az apim](/cli/azure/apim) 命令创建新的 API 管理实例的步骤。 `az apim` 命令组中的命令当前为预览版，在将来的版本中可能会更改或删除。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本快速入门。 如果想要在本地使用它，建议使用 2.11.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

Azure API 管理实例像所有 Azure 资源一样必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

首先，使用以下 [az group create](/cli/azure/group#az-group-create) 命令在“美国中部”位置中创建一个名为“myResourceGroup”的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>创建新服务

现在，你已有了一个资源组，可以创建 API 管理服务实例了。 使用 [az apim create](/cli/azure/apim#az-apim-create) 命令创建一个，并提供服务名称和发布者详细信息。 服务名称在 Azure 中必须独一无二。 

在下面的示例中，使用“myapim”作为服务名称。 将该名称更新为唯一值。 同时更新 API 发布者的组织的名称以及用于接收通知的电子邮件地址。 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

默认情况下，该命令在“开发人员”层创建实例，这是评估 Azure API 管理的一个经济选择。 此层不用于生产。 有关对 API 管理层进行缩放的详细信息，请参阅[升级和缩放](upgrade-and-scale.md)。 

> [!TIP]
> 在此层中创建和激活 API 管理服务可能需要 30 到 40 分钟。 上一命令使用了 `--no-wait` 选项，因此在创建服务后该命令会立即返回。

通过运行 [az apim show](/cli/azure/apim#az-apim-show) 命令检查部署的状态：

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

最初，输出类似于以下内容，显示了 `Activating` 状态：

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

激活后，状态为 `Online`，服务实例有网关地址和公共 IP 地址。 目前，这些地址不会公开任何内容。 例如： 。

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

当 API 管理服务实例处于联机状态时，便可以使用它了。 从[导入并发布第一个 API](import-and-publish.md) 教程开始。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和 API 管理服务实例，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入和发布第一个 API](import-and-publish.md)
