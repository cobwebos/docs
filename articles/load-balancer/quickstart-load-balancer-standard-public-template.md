---
title: 快速入门：创建标准负载均衡器 - Azure 资源管理器模板
titlesuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure 资源管理器模板创建标准负载均衡器。
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480398"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建标准负载均衡器以对 VM 进行负载均衡

可以通过负载均衡将传入请求分布到多个虚拟机，从而提供更高级别的可用性和可伸缩性。 可以部署 Azure 资源管理器模板来创建负载均衡器以对虚拟机 (VM) 进行负载均衡。 本快速入门演示如何使用标准负载均衡器对 VM 进行负载均衡。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

在本部分，我们将创建一个标准负载均衡器，以帮助对虚拟机进行负载均衡。 标准负载均衡器仅支持标准公共 IP 地址。 创建标准负载均衡器时，还必须为该标准负载均衡器创建一个配置为前端（默认情况下命名为 *LoadBalancerFrontend*）的新的标准公共 IP 地址。 有许多方法可用于创建标准负载均衡器。 在本快速入门中，使用 Azure PowerShell 来部署[资源管理器模板](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json)。 Resource Manager 模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 若要了解与部署和管理 Azure 解决方案相关联的概念，请参阅 [Azure 资源管理器文档](/azure/azure-resource-manager/)。 若要查找更多与 Azure 负载均衡器相关的模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

若要部署模板，请选择“试一试”，打开 Azure Cloud shell，然后将以下 PowerShell 脚本粘贴到 shell 窗口中  。 若要粘贴代码，请右键单击 shell 窗口并选择“粘贴”  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

请注意，资源组名称是追加了 **rg** 的项目名称。 在下一部分中将用到资源组名称。  创建资源需要几分钟时间。

## <a name="test-the-load-balancer"></a>测试负载均衡器

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 从左侧窗格中选择“资源组”  。
1. 选择在上一部分中创建的资源组。  默认资源组名称是追加了 **rg** 的项目名称。
1. 选择负载均衡器。  只有一个负载均衡器。 默认名称是追加了 **-lb** 的项目名称。
1. 复制公共 IP 地址（只复制 IP 地址部分），然后将其粘贴到浏览器的地址栏中。 IIS Web 服务器的默认页会显示在浏览器上。

   ![IIS Web 服务器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要查看负载均衡器如何在所有 3 个 VM 之间分配流量，可以从客户端计算机强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源组、负载均衡器和所有相关资源，请将其删除。 为此，请从 Azure 门户中选择包含负载均衡器的资源组，然后选择“删除资源组”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了一个标准负载均衡器，向其附加了 VM，配置了负载均衡器流量规则与运行状况探测，然后测试了该负载均衡器。 若要了解有关 Azure 负载均衡器的详细信息，请继续学习 Azure 负载均衡器教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
