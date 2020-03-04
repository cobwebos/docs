---
title: 在容器实例中设置环境变量
description: 了解如何在 Azure 容器实例的已运行容器中设置环境变量
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252202"
---
# <a name="set-environment-variables-in-container-instances"></a>在容器实例中设置环境变量

通过在容器实例中设置环境变量，可为容器运行的应用程序或脚本提供动态配置。 这类似于在 `--env` 命令行中指定参数 `docker run`。 

若要设置容器中的环境变量，请在创建容器实例时进行指定。 本文介绍了在使用[Azure CLI](#azure-cli-example)、 [Azure PowerShell](#azure-powershell-example)和[Azure 门户](#azure-portal-example)启动容器时设置环境变量的示例。 

例如，如果运行 Microsoft [wordcount][aci-wordcount]容器映像，则可以通过指定以下环境变量来修改其行为：

*NumWords*：发送到 STDOUT 的单词数。

*MinLength*：单词中最少包含几个字符才将它统计为一个单词。 如果指定较大的数字，将会忽略“of”和“the”等常见单词。

如果需要以环境变量的形式传递机密，Azure 容器实例支持 Windows 和 Linux 容器的[安全值](#secure-values)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI 示例

若要查看[wordcount][aci-wordcount]容器的默认输出，请先通过此[az container create][az-container-create]命令运行它（未指定环境变量）：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

若要修改输出，请在添加 `--environment-variables` 变量的情况下启动又一个容器，为 *NumWords* 和 *MinLength* 变量指定值。 （此示例假定在 Bash shell 或 Azure Cloud Shell 中运行 CLI。 如果使用 Windows 命令提示符，请使用双引号指定变量，例如 `--environment-variables "NumWords"="5" "MinLength"="8"`。）

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

当两个容器的状态显示为 "已*终止*" 时（使用[az container show][az-container-show]来检查状态），使用[az 容器日志][az-container-logs]显示其日志以查看输出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

容器的输出显示你如何通过设置环境变量修改了第二个容器的脚本行为。

**mycontainer1**
```output
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

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 示例

在 PowerShell 中设置环境变量类似于在 CLI 中进行的相应操作，但需使用 `-EnvironmentVariable` 命令行参数。

首先，通过以下[AzContainerGroup][new-Azcontainergroup]命令在其默认配置中启动[wordcount][aci-wordcount]容器：

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

现在，请运行以下[AzContainerGroup][new-Azcontainergroup]命令。 此命令在填充数组变量 *后指定*NumWords*和*MinLength`envVars` 环境变量：

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

*终止*两个容器的状态后（使用[AzContainerInstanceLog][azure-instance-log]检查状态），使用[AzContainerInstanceLog][azure-instance-log]命令拉取其日志。

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

每个容器的输出显示你如何通过设置环境变量修改了容器运行的脚本。

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure 门户示例

若要在 Azure 门户中启动容器时设置环境变量，请在创建容器时在 "**高级**" 页中指定它们。

1. 在 "**高级**" 页上，将 "**重新启动策略**" 设置为 *"失败时*"
2. 在 "**环境变量**" 下，输入第一个变量的值为 `5` `NumWords`，并输入 `MinLength`，并将第二个变量的值替换为 "`8`"。 
1. 选择 "**查看 + 创建**"，验证并部署容器。

![门户页，显示环境变量“启用”按钮和文本框][portal-env-vars-01]

要查看容器的日志，请在 "**设置**" 下选择 "**容器**"，然后选择 "**日志**"。 可以查看通过环境变量对脚本行为进行的具体修改，这与在此前的 CLI 和 PowerShell 部分显示的输出类似。 仅显示了五个单词，每个的最小长度为八个字符。

![显示容器日志输出的门户][portal-env-vars-02]

## <a name="secure-values"></a>安全值

具有安全值的对象旨在为应用程序保留密码或密钥等敏感信息。 对环境变量使用安全值比将它添加到容器映像中更为安全灵活。 也可以使用机密卷，如[在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)所述。

具有安全值的环境变量在容器的属性中不可见 - 它们的值只能从容器中访问。 例如，在 Azure 门户或 Azure CLI 中查看的容器属性仅显示安全变量的名称，而不显示其值。

安全环境变量的设置方法为，指定 `secureValue` 属性（而不是常规 `value`）作为变量类型。 下面 YAML 中定义的两个变量展示了两种变量类型。

### <a name="yaml-deployment"></a>YAML 部署

创建包含下面的代码片段的 `secure-env.yaml` 文件。

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

运行以下命令以使用 YAML 部署容器组（根据需要调整资源组名称）：

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>验证环境变量

运行[az container show][az-container-show]命令以查询容器的环境变量：

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON 响应显示不安全的环境变量的键和值，但仅显示安全环境变量的名称：

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

使用[az container exec][az-container-exec]命令，可在正在运行的容器中执行命令，你可以验证是否已设置安全环境变量。 运行以下命令以在容器中启动交互式 bash 会话：

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

在容器中打开交互式 shell 后，可以访问 `SECRET` 变量的值：

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>后续步骤

基于任务的方案（例如对使用多个容器的数据库进行批处理）可以在运行时充分利用自定义环境变量。 有关运行基于任务的容器的详细信息，请参阅[运行包含重新启动策略的容器化任务](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
