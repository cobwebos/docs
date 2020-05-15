---
title: 快速入门 - 使用 Azure CLI 创建虚拟机规模集
description: 通过了解如何使用 Azure CLI 快速创建虚拟机规模集来开始部署。
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: af69b4d80ef6f7216bdf13a7e7314e8b0616f376
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197718"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 然后，Azure 负载均衡器会将流量分配到规模集中的 VM 实例。 在本快速入门中，我们将使用 Azure CLI 创建虚拟机规模集并部署一个示例应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="create-a-scale-set"></a>创建规模集
使用 [az group create](/cli/azure/group) 创建资源组，才能创建规模集。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vmss create](/cli/azure/vmss) 创建虚拟机规模集。 以下示例创建名为 *myScaleSet* 的规模集，该规模集设置为在应用更改时自动更新；如果 *~/.ssh/id_rsa* 中没有 SSH 密钥，此示例还会生成 SSH 密钥。 登录到 VM 实例时需要使用这些 SSH 密钥。 若要使用一组现有的 SSH 密钥，请改用 `--ssh-key-value` 参数并指定密钥的位置。

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


## <a name="deploy-sample-application"></a>部署示例应用程序
若要测试规模集，请安装一个基本的 Web 应用程序。 使用 Azure 自定义脚本扩展下载并运行一个脚本，以便在 VM 实例上安装应用程序。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/linux/extensions-customscript.md)。

使用自定义脚本扩展安装基本的 NGINX Web 服务器。 使用 [az vmss extension set](/cli/azure/vmss/extension) 应用可安装 NGINX 的自定义脚本扩展，如下所示：

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>允许流量发往应用程序
创建规模集时，已自动部署一个 Azure 负载均衡器。 该负载均衡器会将流量分配到规模集中的 VM 实例。 若要允许流量抵达示例 Web 应用程序，请使用 [az network lb rule create](/cli/azure/network/lb/rule) 创建一个负载均衡器规则。 以下示例创建名为“myLoadBalancerRuleWeb”  的规则：

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


## <a name="test-your-scale-set"></a>测试规模集
若要查看正在运行的规模集，请在 Web 浏览器中访问示例 Web 应用程序。 使用 [az network public-ip show](/cli/azure/network/public-ip) 获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的“myScaleSetLBPublicIP”  的 IP 地址：

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![NGINX 中的默认网页](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>清理资源
如果不再需要资源组、规模集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除，如下所示。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>后续步骤
在本快速入门中，我们已创建一个基本的规模集，并使用自定义脚本扩展在 VM 实例上安装了一个基本的 NGINX Web 服务器。 若要了解详细信息，请继续学习有关如何创建和管理 Azure 虚拟机规模集的教程。

> [!div class="nextstepaction"]
> [创建和管理 Azure 虚拟机规模集](tutorial-create-and-manage-cli.md)

