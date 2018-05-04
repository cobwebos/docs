---
title: 在 Azure 容器实例中设置环境变量
description: 了解如何在 Azure 容器实例中设置环境变量
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>设置环境变量。

通过在容器实例中设置环境变量，可为容器运行的应用程序或脚本提供动态配置。

目前能够通过 CLI 和 PowerShell 设置环境变量。 在这两种情况下，都会使用命令上的标志来设置环境变量。 在 ACI 中设置环境变量类似于设置 `docker run` 的 `--env` 命令行参数。 例如，如果使用 microsoft/aci-wordcount 容器映像，可以通过指定以下环境变量来修改行为：

*NumWords*：发送到 STDOUT 的单词数。

*MinLength*：单词中最少包含几个字符才将它统计为一个单词。 如果指定较大的数字，将会忽略“of”和“the”等常见单词。

## <a name="azure-cli-example"></a>Azure CLI 示例

若要查看容器的默认输出，请运行以下命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

对容器的环境变量指定 `NumWords=5` 和 `MinLength=8` 后，容器日志应会显示不同的输出。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

容器状态显示为 *Terminated*（使用 [az container show][az-container-show] 检查其状态）后，将显示其日志用于查看输出。  若要不使用环境变量查看容器的输出，请将 --name 设为 mycontainer1 而不是 mycontainer2。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Azure PowerShell 示例

若要查看容器的默认输出，请运行以下命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

对容器的环境变量指定 `NumWords=5` 和 `MinLength=8` 后，容器日志应会显示不同的输出。

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

容器状态为 *Terminated*（使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 检查其状态）后，将显示其日志用于查看输出。 若要不使用环境变量查看容器日志，请将 ContainerGroupName 设为 mycontainer1 而不是 mycontainer2。

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>不使用环境变量的示例输出

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>使用环境变量的示例输出

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>后续步骤

既然已了解如何自定义容器的输入，请了解如何持久保存运行到完成的容器输出。
> [!div class="nextstepaction"]
> [使用 Azure 容器实例装载 Azure 文件共享](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show