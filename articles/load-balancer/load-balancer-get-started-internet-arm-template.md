---
title: "使用模板在 Resource Manager 中创建面向 Internet 的负载均衡器 | Microsoft Docs"
description: "了解如何使用模板在 Resource Manager 中创建面向 Internet 的负载平衡器"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 47a057a5ff7fc893a2f82d762db499ec10a8db8e

---

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>使用模板创建面向 Internet 的负载平衡器

> [!div class="op_single_selector"]
> * [门户](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [模板](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍资源管理器部署模型。 还可以[了解如何使用经典部署模型创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>通过单击部署方式部署模板

公共存储库中提供的示例模板采用包含用于生成上述方案的默认值的参数文件。 若要通过单击部署的方式来部署此模板，请访问[此链接](http://go.microsoft.com/fwlink/?LinkId=544801)，单击“部署至 Azure”，如有必要，请替换默认参数值，然后按照门户中的说明进行操作。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署模板

若要使用 PowerShell 部署下载的模板，请执行以下步骤。

1. 如果你从未使用过 Azure PowerShell，请参阅 [How to Install and Configure Azure PowerShell](/powershell/azureps-cmdlets-docs) （如何安装和配置 Azure PowerShell），并始终按照说明进行操作，以登录到 Azure 并选择你的订阅。
2. 运行 **New-AzureRmResourceGroupDeployment** cmdlet 以使用模板创建资源组。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署模板

若要使用 Azure CLI 部署模板，请执行以下步骤。

1. 如果你从未使用过 Azure CLI，请参阅 [安装和配置 Azure CLI](../xplat-cli-install.md) ，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **azure config mode** 命令以切换到资源管理器模式，如下所示。

    ```azurecli
    azure config mode arm
    ```

    下面是上述命令的预期输出：

        info:    New mode is arm

3. 从浏览器中，导航到[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)，复制 json 文件的内容并粘贴到计算机中的一个新文件。 对于此方案，需要将下面的值复制到名为 **c:\lb\azuredeploy.parameters.json** 的文件。
4. 运行 **azure group deployment create** cmdlet 以使用在前面下载并修改的模板和参数文件部署新的负载均衡器。 在输出后显示的列表说明了所用的参数。

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>后续步骤

[开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Dec16_HO1-->


