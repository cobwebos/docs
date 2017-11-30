---
title: "创建内部负载均衡器 - Azure 模板 | Microsoft 文档"
description: "了解如何在 Resource Manager 中使用模板创建内部负载均衡器"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 7abb8fb5064ca2ab1e4da6e0a32335f63d87ea02
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>使用模板创建内部负载均衡器

> [!div class="op_single_selector"]
> * [Azure 门户](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [模板](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>通过单击部署方式部署模板

公共存储库中提供的示例模板采用包含用于生成上述方案的默认值的参数文件。 要通过单击部署的方式来部署此模板，请访问[此链接](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)，单击“部署至 Azure”，如有必要，请替换默认参数值，并按照门户中的说明进行操作。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署模板

若要使用 PowerShell 部署下载的模板，请执行以下步骤。

1. 如果从未使用过 Azure PowerShell，请参阅 [How to Install and Configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell），并始终按照说明进行操作，以登录到 Azure 并选择订阅。
2. 将参数文件下载到本地磁盘。
3. 编辑该文件并将其保存。
4. 运行 **New-AzureRmResourceGroupDeployment** cmdlet 以使用模板创建资源组。

    ```azurecli
    New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署模板

若要使用 Azure CLI 部署模板，请执行以下步骤。

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../cli-install-nodejs.md)，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **azure config mode** 命令以切换到 Resource Manager 模式，如下所示。

    ```azurecli
    azure config mode arm
    ```

    下面是上述命令的预期输出：

        info:    New mode is arm

3. 打开参数文件，选择其内容，然后将其保存到计算机上的文件中。 对于本示例，我们将参数文件保存到 *parameters.json*。
4. 运行 **azure group deployment create** 命令以使用之前下载并修改的模板和参数文件部署新的内部负载均衡器。 在输出后显示的列表说明了所用的参数。

    ```azurecli
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>后续步骤

[使用源 IP 关联配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)

