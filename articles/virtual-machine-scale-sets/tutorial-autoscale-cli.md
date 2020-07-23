---
title: 教程 - 使用 Azure CLI 自动缩放规模集
description: 了解如何使用 Azure CLI 随 CPU 需求的增减自动缩放虚拟机规模集
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 3f51c4e50c3c5499c73e18ce40e55de6aeb54472
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700891"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>教程：使用 Azure CLI 自动缩放虚拟机规模集

创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 对规模集使用自动缩放
> * 创建和使用自动缩放规则
> * 对 VM 实例进行压力测试并触发自动缩放规则
> * 在需求下降时自动横向缩减

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.32 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-scale-set"></a>创建规模集

使用 [az group create](/cli/azure/group) 创建资源组，如下所示：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vmss create](/cli/azure/vmss) 创建虚拟机规模集。 以下示例创建实例计数为 *2* 的规模集，并生成 SSH 密钥（如果不存在）：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>定义自动缩放配置文件

若要在规模集上启用自动缩放，首先要定义自动缩放配置文件。 此配置文件定义默认、最小和最大规模集容量。 这些限制可让你通过不继续创建 VM 实例来控制成本，并可使用缩小事件中保留的最小数量的实例均衡可接受的性能。 使用 [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create) 创建自动缩放配置文件。 以下示例设置了默认值，以及最小容量 2 个 VM 实例、最大容量 10 个 VM ：

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>创建规则，以便自动横向扩展

如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例并部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

让我们使用 [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) 创建一个规则，当平均 CPU 负载在 5 分钟内超过 70% 时，该规则会增加规模集中的 VM 实例数。 触发规则时，VM 实例数增加 3。

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>创建规则，以便自动横向缩减

在夜间或周末，应用程序需求可能会降低。 如果这种负载降低在一段时间内持续稳定，可以配置自动缩放规则来减少规模集中的 VM 实例数。 这种横向缩减操作可以减少运行规模集所需的成本，因为只运行满足当前需求所需的实例数。

让我们使用 [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) 创建另一个规则，当平均 CPU 负载随后在 5 分钟内低于 30% 时，该规则会减少规模集中的 VM 实例数。 以下示例定义将 VM 实例数横向缩减 1 的规则：

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>在规模集上生成 CPU 负载

若要测试自动缩放规则，请在规模集的 VM 实例上生成一些 CPU 负载。 这种模拟的 CPU 负载会导致自动缩放以横向扩展的方式增加 VM 实例数。 随着模拟的 CPU 负载下降，自动缩放规则会进行横向缩减，减少 VM 实例数。

首先，请使用 [az vmss list-instance-connection-info](/cli/azure/vmss) 列出用于连接到规模集中的 VM 实例的地址和端口：

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

以下示例输出显示了实例名称、负载均衡器的公共 IP 地址，以及可以通过网络地址转换 (NAT) 规则将流量转发到的端口号：

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

通过 SSH 连接到第一个 VM 实例。 使用 `-p` 参数指定自己的公共 IP 地址和端口号，如前述命令所示：

```console
ssh azureuser@13.92.224.66 -p 50001
```

登录后，安装 **stress** 实用工具。 启动 10 个生成 CPU 负载的 **stress** 辅助角色。 这些辅助角色运行 *420* 秒，此时间足以让自动缩放规则实施所需的操作。

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

当 **stress** 显示类似于 *stress: info: [2688] dispatching hogs:10 cpu, 0 io, 0 vm, 0 hdd* 的输出时，按 *Enter* 键返回到提示符。

若要确认 **stress** 是否生成了 CPU 负载，请使用 **top** 实用工具检查活动的系统负载：

```console
top
```

退出 **top**，然后关闭与 VM 实例的连接。 **stress** 继续在 VM 实例上运行。

```console
Ctrl-c
exit
```

连接到第二个 VM 实例，所使用的端口号是前面的 [az vmss list-instance-connection-info](/cli/azure/vmss) 列出的：

```console
ssh azureuser@13.92.224.66 -p 50003
```

安装并运行 **stress**，然后在这第二个 VM 实例上启动十个辅助角色。

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

当 **stress** 再次显示类似于 *stress: info: [2713] dispatching hogs:10 cpu, 0 io, 0 vm, 0 hdd* 的输出时，按 *Enter* 键返回到提示符。

关闭与第二个 VM 实例的连接。 **stress** 继续在 VM 实例上运行。

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>监视活动的自动缩放规则

若要监视规模集中的 VM 实例数，请使用 **watch**。 自动缩放规则需要 5 分钟的时间才能开始横向扩展过程，以便响应由每个 VM 实例上的 **stress** 生成的 CPU 负载：

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

达到 CPU 阈值以后，自动缩放规则会增加规模集中的 VM 实例数。 以下输出显示，在规模集进行自动横向扩展时创建了 3 个 VM：

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

当 **stress** 在初始 VM 实例上停止后，平均 CPU 负载会回到正常。 另一个 5 分钟后，自动缩放规则会横向缩减 VM 实例数。 横向缩减操作会首先删除 ID 值最高的 VM 实例。 如果规模集使用可用性集或可用性区域，则横向缩减操作将均匀分布到这些 VM 实例上。 以下示例输出显示，在规模集进行自动横向缩减时删除了一个 VM 实例：

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

使用 `Ctrl-c` 退出 *watch*。 规模集继续每 5 分钟横向缩减一次，每次删除一个 VM 实例，直至达到最小实例计数 2。

## <a name="clean-up-resources"></a>清理资源

若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Azure CLI 自动进行规模集的横向缩减或扩展：

> [!div class="checklist"]
> * 对规模集使用自动缩放
> * 创建和使用自动缩放规则
> * 对 VM 实例进行压力测试并触发自动缩放规则
> * 在需求下降时自动横向缩减
