---
title: 教程 - 使用 Azure 模板在规模集中安装应用程序 | Microsoft Docs
description: 了解如何使用自定义脚本扩展通过 Azure 资源管理器模板将应用程序安装到虚拟机规模集中
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bc55fc7822b8f60d12ad8d8d8c36c21c1f602f74
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606262"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>教程：使用 Azure 模板在虚拟机规模集中安装应用程序
若要在规模集中的虚拟机 (VM) 实例上运行应用程序，首先需要安装应用程序组件和所需文件。 前一篇教程介绍了如何创建自定义 VM 映像并使用它来部署 VM 实例。 使用此自定义映像可以手动安装和配置应用程序。 也可以在部署每个 VM 实例之后，将应用程序自动安装到规模集，或者更新已在规模集中运行的应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。


## <a name="what-is-the-azure-custom-script-extension"></a>什么是 Azure 自定义脚本扩展？
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以与 Azure CLI 2.0、Azure PowerShell、Azure 门户或 REST API 配合使用。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/linux/extensions-customscript.md)。

若要查看自定义脚本扩展的运行方式，请创建一个可以安装 NGINX Web 服务器并输出规模集 VM 实例主机名的规模集。 以下自定义脚本扩展定义从 GitHub 下载示例脚本，安装所需的包，然后将 VM 实例主机名写入一个基本的 HTML 页面。


## <a name="create-custom-script-extension-definition"></a>创建自定义脚本扩展定义
使用 Azure 模板定义虚拟机规模集时，*Microsoft.Compute/virtualMachineScaleSets* 资源提供程序可在扩展中包含一个节。 *extensionsProfile* 详细描述要将哪些设置应用到规模集中的 VM 实例。 若要使用自定义脚本扩展，需指定类型为 *CustomScript* 的 *Microsoft.Azure.Extensions* 的发布者。

*fileUris* 属性用于定义源安装脚本或包。 若要启动安装过程，请在 *commandToExecute* 中定义所需的脚本。 以下示例定义 GitHub 中的一个示例脚本，该脚本可安装并配置 NGINX Web 服务器：

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

有关可以部署规模集和自定义脚本扩展的 Azure 模板的完整示例，请参阅 [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json)。 下一部分将使用此示例模板。


## <a name="create-a-scale-set"></a>创建规模集
让我们使用示例模板创建一个规模集并应用自定义脚本扩展。 首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

现在，使用 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) 创建虚拟机规模集。 出现提示时，请提供自己的、用作每个 VM 实例的凭据的用户名和密码：

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。

规模集中的每个 VM 实例将下载并运行 GitHub 中的脚本。 在更复杂的示例中，可以安装多个应用程序组件和文件。 如果规模集已纵向扩展，则新 VM 实例会自动应用相同的自定义脚本扩展定义，并安装所需的应用程序。


## <a name="test-your-scale-set"></a>测试规模集
若要查看运行中的 Web 服务器，请使用 [az network public-ip show](/cli/azure/network/public-ip#show) 命令获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的 *myScaleSetPublicIP* 的 IP 地址：

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![Nginx 中的基本网页](media/tutorial-install-apps-template/running-nginx.png)

让 Web 浏览器保持打开状态，以便在下一步骤中可以看到更新的版本。


## <a name="update-app-deployment"></a>更新应用部署
在规模集的整个生命周期内，都可能需要部署应用程序的更新版本。 使用自定义脚本扩展可以引用更新的部署脚本，然后将扩展重新应用到规模集。 在上一步骤中创建规模集时，*upgradePolicy` 已设置为 *Automatic*。 此设置可让规模集中的 VM 实例自动更新应用程序并应用其最新版本。

若要更新自定义脚本扩展定义，请编辑模板以引用新的安装脚本。 必须对自定义脚本扩展使用新文件名，以识别更改。 自定义脚本扩展不会通过检查脚本的内容来确定是否发生了任何更改。 以下定义使用更新的安装脚本，该脚本的名称后面追加了 *_v2*：

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

使用 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) 再次将自定义脚本扩展配置应用到规模集中的 VM 实例。 此 *azuredeployv2.json* 模板用于应用更新版本的应用程序。 在实践中，请根据前一部分中所示编辑现有的 *azuredeploy.json* 模板，以引用更新的安装脚本。 出现提示时，请输入首次创建规模集时使用的相同用户名和密码凭据：

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

规模集中的所有 VM 实例将使用示例网页的最新版本自动更新。 若要查看更新的版本，请在浏览器中刷新网站：

![Nginx 中更新的网页](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用 Azure 模板在规模集中自动安装和更新应用程序：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

请继续学习下一教程，了解如何自动缩放规模集。

> [!div class="nextstepaction"]
> [自动缩放规模集](tutorial-autoscale-template.md)
