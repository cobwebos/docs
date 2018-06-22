---
title: 在 Azure 容器实例中设置环境变量
description: 了解如何在 Azure 容器实例的已运行容器中设置环境变量
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/07/2018
ms.author: marsma
ms.openlocfilehash: bc30352f50344031f8356d2be1b800dd035f12ad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830456"
---
# <a name="set-environment-variables"></a>设置环境变量。

通过在容器实例中设置环境变量，可为容器运行的应用程序或脚本提供动态配置。 若要设置容器中的环境变量，请在创建容器实例时进行指定。 可以在使用 [Azure CLI](#azure-cli-example)、[Azure PowerShell](#azure-powershell-example) 和 [Azure 门户](#azure-portal-example)启动容器时设置环境变量。

例如，如果运行 [microsoft/aci-wordcount][aci-wordcount] 容器映像，可以通过指定以下环境变量来修改其行为：

*NumWords*：发送到 STDOUT 的单词数。

*MinLength*：单词中最少包含几个字符才将它统计为一个单词。 如果指定较大的数字，将会忽略“of”和“the”等常见单词。

如果需要以环境变量的形式传递机密，Azure 容器实例支持 Windows 和 Linux 容器的[安全值](#secure-values)。

## <a name="azure-cli-example"></a>Azure CLI 示例

若要查看 [microsoft/aci-wordcount][aci-wordcount] 容器的默认输出，请首先使用此 [az container create][az-container-create] 命令来运行它（不指定环境变量）：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

若要修改输出，请在添加 `--environment-variables` 变量的情况下启动又一个容器，为 *NumWords* 和 *MinLength* 变量指定值：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

两个容器的状态显示为“已终止”后（使用 [az container show][az-container-show] 来查看状态），请使用 [az container logs][az-container-logs] 来显示其日志，以便查看输出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

容器的输出显示你如何通过设置环境变量修改了第二个容器的脚本行为。

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 示例

在 PowerShell 中设置环境变量类似于在 CLI 中进行的相应操作，但需使用 `-EnvironmentVariable` 命令行参数。

首先，使用此 [New-AzureRmContainerGroup][new-azurermcontainergroup] 命令在默认配置中启动 [microsoft/aci-wordcount][aci-wordcount] 容器：

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

现在请运行以下 [New-AzureRmContainerGroup][new-azurermcontainergroup] 命令。 此命令在填充数组变量 `envVars` 后指定 *NumWords* 和 *MinLength* 环境变量：

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

两个容器的状态均为“已终止”后（使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 来查看状态），请使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 命令来拉取其日志。

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

每个容器的输出显示你如何通过设置环境变量修改了容器运行的脚本。

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure 门户示例

在 Azure 门户中启动容器时，若要设置环境变量，请在创建容器时所在的“配置”页中指定它们。

使用门户进行部署时，目前仅限使用三个变量，并且必须以 `"variableName":"value"` 格式输入它们

若要查看示例，请使用 *NumWords* 和 *MinLength* 变量启动 [microsoft/aci-wordcount][aci-wordcount] 容器。

1. 在“配置”中将“重启策略”设置为“在故障时”
2. 为第一个变量输入 `"NumWords":"5"`，在“添加其他环境变量”下选择“是”，然后为第二个变量输入 `"MinLength":"8"`。 选择“确定”进行确认，然后部署容器。

![门户页，显示环境变量“启用”按钮和文本框][portal-env-vars-01]

若要查看容器的日志，请在“设置”下选择“容器”，然后选择“日志”。 可以查看通过环境变量对脚本行为进行的具体修改，这与在此前的 CLI 和 PowerShell 部分显示的输出类似。 仅显示了五个单词，每个的最小长度为八个字符。

![显示容器日志输出的门户][portal-env-vars-02]

## <a name="secure-values"></a>安全值
具有安全值的对象旨在为应用程序保留密码或密钥等敏感信息。 对环境变量使用安全值比将它添加到容器映像中更为安全灵活。 也可以使用机密卷，如[在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)所述。

使用安全值保护环境变量不会在容器属性中显示安全值，这样就只能在容器内访问安全值了。 例如，在 Azure 门户或 Azure CLI 中查看容器属性时，就不会看到具有安全值的环境变量。

安全环境变量的设置方法为，指定 `secureValue` 属性（而不是常规 `value`）作为变量类型。 下面 YAML 中定义的两个变量展示了两种变量类型。

### <a name="yaml-deployment"></a>YAML 部署

运行下面的代码片段，创建 `secure-env.yaml` 文件。

```yaml
apiVersion: 2018-06-01
location: westus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "SECRET"
          "secureValue": "my-secret-value"
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
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

运行下面的命令，使用 YAML 部署容器组。

```azurecli-interactive
az container create --resource-group myRG --name securetest -f secure-env.yaml
```

### <a name="verify-environment-variables"></a>验证环境变量

运行下面的命令，查询是否有容器的环境变量。

```azurecli-interactive
az container show --resource-group myRG --name securetest --query 'containers[].environmentVariables`
```

包含此容器详细信息的 JSON 响应只显示非安全环境变量和安全环境变量的密钥。

```json
  "environmentVariables": [
    {
      "name": "NOTSECRET",
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET"
    }
```

可以使用 `exec` 命令查看所设置的安全环境变量，此命令便于在正在运行的容器中执行命令。 

运行下面的命令，启动与容器的交互式 bash 会话。
```azurecli-interactive
az container exec --resource-group myRG --name securetest --exec-command "/bin/bash"
```

在容器中，运行下面的 bash 命令，打印出环境变量。
```bash
echo $SECRET
```

## <a name="next-steps"></a>后续步骤

基于任务的方案（例如对使用多个容器的数据库进行批处理）可以在运行时充分利用自定义环境变量。 若要详细了解如何运行基于任务的容器，请参阅[在 Azure 容器实例中运行容器化任务](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
