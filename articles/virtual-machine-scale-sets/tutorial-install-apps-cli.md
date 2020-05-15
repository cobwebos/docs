---
title: 教程 - 使用 Azure CLI 在规模集中安装应用程序
description: 了解如何使用自定义脚本扩展通过 Azure CLI 将应用程序安装到虚拟机规模集中
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 56aa67750a4ec51704a440424bfeef15e8806163
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197106"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>教程：使用 Azure CLI 在虚拟机规模集中安装应用程序
若要在规模集中的虚拟机 (VM) 实例上运行应用程序，首先需要安装应用程序组件和所需文件。 前一篇教程介绍了如何创建自定义 VM 映像并使用它来部署 VM 实例。 使用此自定义映像可以手动安装和配置应用程序。 也可以在部署每个 VM 实例之后，将应用程序自动安装到规模集，或者更新已在规模集中运行的应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="what-is-the-azure-custom-script-extension"></a>什么是 Azure 自定义脚本扩展？
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以与 Azure CLI、Azure PowerShell、Azure 门户或 REST API 配合使用。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/linux/extensions-customscript.md)。

若要通过 Azure CLI 使用自定义脚本扩展，请创建 JSON 文件，用于定义要包含的文件和要执行的命令。 这些 JSON 定义可以在规模集部署之间重复使用，以应用一致的应用程序安装。


## <a name="create-custom-script-extension-definition"></a>创建自定义脚本扩展定义
若要查看自定义脚本扩展的运行方式，请创建一个可以安装 NGINX Web 服务器并输出规模集 VM 实例主机名的规模集。 以下自定义脚本扩展定义从 GitHub 下载示例脚本，安装所需的包，然后将 VM 实例主机名写入一个基本的 HTML 页面。

在当前 shell 中，创建名为“customConfig.json”的文件并粘贴下面的配置  。 例如，在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 在 Cloud Shell 中输入 `sensible-editor customConfig.json`，以创建文件并查看可用编辑器的列表。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>创建规模集
使用 [az group create](/cli/azure/group) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vmss create](/cli/azure/vmss) 创建虚拟机规模集。 以下示例创建名为“myScaleSet”  的规模集，并生成 SSH 密钥（如果不存在）：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="apply-the-custom-script-extension"></a>应用自定义脚本扩展
使用 [az vmss extension set](/cli/azure/vmss/extension) 将自定义脚本扩展配置应用到规模集中的 VM 实例。 以下示例将 customConfig.json 配置应用于名为 myResourceGroup 的资源组中的 myScaleSet VM 实例    ：

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

规模集中的每个 VM 实例将下载并运行 GitHub 中的脚本。 在更复杂的示例中，可以安装多个应用程序组件和文件。 如果规模集已纵向扩展，则新 VM 实例会自动应用相同的自定义脚本扩展定义，并安装所需的应用程序。


## <a name="test-your-scale-set"></a>测试规模集
若要允许通信流到达 Web 服务器，请使用 [az network lb rule create](/cli/azure/network/lb/rule) 命令创建负载均衡器规则。 以下示例创建名为“myLoadBalancerRuleWeb”  的规则：

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

若要查看运行中的 Web 服务器，请使用 [az network public-ip show](/cli/azure/network/public-ip) 命令获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的“myScaleSetLBPublicIP”  的 IP 地址：

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![Nginx 中的基本网页](media/tutorial-install-apps-cli/running-nginx.png)

让 Web 浏览器保持打开状态，以便在下一步骤中可以看到更新的版本。


## <a name="update-app-deployment"></a>更新应用部署
在规模集的整个生命周期内，都可能需要部署应用程序的更新版本。 使用自定义脚本扩展可以引用更新的部署脚本，然后将扩展重新应用到规模集。 在上一步骤中创建规模集时，`--upgrade-policy-mode` 已设置为 *automatic*。 此设置可让规模集中的 VM 实例自动更新应用程序并应用其最新版本。

在当前 shell 中，创建名为“customConfigv2.json”的文件并粘贴下面的配置  。 此定义运行应用程序安装脚本的 *v2* 更新版本：

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

使用 [az vmss extension set](/cli/azure/vmss/extension) 再次将自定义脚本扩展配置应用到规模集中的 VM 实例。 *customConfigv2.json* 用于应用更新版本的应用程序。

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

规模集中的所有 VM 实例将使用示例网页的最新版本自动更新。 若要查看更新的版本，请在浏览器中刷新网站：

![Nginx 中更新的网页](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>后续步骤
本教程介绍了如何使用 Azure CLI 在规模集中自动安装和更新应用程序：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

请继续学习下一教程，了解如何自动缩放规模集。

> [!div class="nextstepaction"]
> [自动缩放规模集](tutorial-autoscale-cli.md)
