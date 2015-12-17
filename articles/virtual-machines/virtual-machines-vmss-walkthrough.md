<properties
	pageTitle="自动缩放虚拟机缩放集 | Microsoft Azure"
	description="开始创建和管理你的第一个 Azure 虚拟机缩放集"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="11/11/2015"
	wacn.date=""/>

# 自动缩放虚拟机缩放集中的虚拟机

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](/documentation/articles/virtual-machines-create-windows-powershell-service-manager)。


本文档是一个快速指南，可指导你在公共预览期间开始创建和管理你的第一个 Azure 虚拟机缩放集。

本指南假定你已熟悉如何使用 Azure 资源管理器 (ARM) 模板来部署虚拟机和 VNET 等 Azure 资源。如果尚不熟悉，请使用“资源”部分中的前 3 个链接大致了解资源管理器。

## 什么是 VM 缩放集，为什么要使用它们？

VM 缩放集是一种 Azure 计算资源，可用于部署和管理一组相同的 VM。

VM 缩放集中的所有 VM 均采用相同的配置，专用于支持真正的自动缩放，而无需对 VM 进行预配，这有助于简化生成面向大计算、大数据、容器化工作负荷以及需要扩大和缩小计算资源的任何应用程序的大规模服务。缩放操作在容错域和更新域中隐式平衡。有关 VM 缩放集的简介，请参阅最近的 [Azure 博客通知](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)。

## 创建和使用 VM 缩放集

可以使用 JSON 模板和 REST 调用定义和部署 VM 缩放集，就像定义和部署单个 Azure 资源管理器 VM 一样。因此，可以使用任何标准的 Azure 资源管理器部署方法，并且本文档将带你了解一些示例。

特定 VMSS 集成区域（例如脚本和编程语言中的强制性命令支持）以及完整门户集成正在开发过程中，将在预览期间分阶段发布。

## 使用 Azure 门户部署和管理 VM 缩放集

最初，你不能在 Azure 门户中从头开始创建 VM 缩放集。门户支持的开发工作仍在进行中，在此工作的第一阶段，你只能在门户中看到已创建的缩放集，并且将必须使用模板/脚本方法来创建它们。在所有情况下，假定“门户”表示新的 Azure 门户：[https://manage.windowsazure.cn](https://manage.windowsazure.cn)。

但是，你可以利用门户部署模板的能力来部署任何资源，包括缩放集。部署简单缩放集的一种简单方法是使用以下形式的链接：

_https://manage.windowsazure.cn#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

可以在此处的 Azure 快速入门模板 GitHub 存储库中找到一组 VM 缩放集的示例模板：[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - 查找标题中含有 _vmss_ 的模板。

这些模板的自述文件中包含一个以下形式的按钮，使用该按钮可链接到门户部署功能。

若要部署缩放集，请单击该按钮，然后填写门户中所需的任何参数。注意：如果你不确定某个资源是否支持大写或大小写混合，则更为安全的做法是始终使用小写参数值。

**在门户/资源管理器中监视 VM 缩放集资源**

部署缩放集后，门户中将显示其基本视图，但最初在预览期间它不会显示详细的属性，也不允许你向下钻取在 VM 缩放集中运行的 VM。

在完整的门户集成就位之前，建议你使用 **Azure 资源管理器** ([https://resources.azure.com](https://resources.azure.com)) 来查看 VM 缩放集。VM 缩放集是 Microsoft.Compute 下的资源，因此，你可以通过在此站点中展开以下链接来查看它们：

订阅 -> 你的订阅 -> resourceGroups -> 提供程序 -> Microsoft.Compute -> virtualMachineScaleSets -> 你的 VM 缩放集 -> 等等

## 使用 PowerShell 部署和管理 VM 缩放集

你可以使用任何当前的 Azure PowerShell 版本部署 VMSS 模板和查询资源。

**重要说明：**下面显示的示例适用于 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/)，它们使用 _AzureRm_ 作为许多命令的前缀。如果正在使用早期版本的 PowerShell（如 0.9.8 或更低版本），请从示例命令中删除“**Rm**”。这些示例还假定你已在 PowerShell 环境中建立与 Azure 的登录连接 (_Login-AzureRmAccount_)。

示例:

&#35; **创建资源组**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **创建包含两个 VM 的 VM 缩放集**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

系统将提示你提供缺少的任何参数（如此示例中的位置、用户名和密码）。

&#35; **获取 VM 缩放集详细信息**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; 或者获取有关管道传输的更多详细信息 | ConvertTo-Json -Depth 10

& #35;或者获取有关向命令添加 –debug 的更多详细信息。

&#35; 扩大或缩小

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **删除缩放集**

&#35; 简单：删除整个资源组以及其中的所有内容：

Remove-AzureRmResourceGroup -Name myrg

&#35; 精确：删除特定资源：

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### 缩放集的强制性 PowerShell 命令

即将推出的 Azure PowerShell 版本将包含一套强制性命令，可用于在不使用模板的情况下创建和管理 VM 缩放集。可在此处获得此版本的预览版：[https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases)。可在此处找到有关如何使用强制性 API 的示例：

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## 使用 Azure CLI 部署和管理 VM 缩放集

你可以使用任何当前的 Azure CLI 版本部署 VMSS 模板和查询资源。

最简单的 CLI 安装方法是从 Docker 容器中进行安装。有关安装信息，请参阅：[https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

有关使用 CLI 的信息，请参阅：[/documentation/articles/xplat-cli/](/documentation/articles/xplat-cli/)

### VM 缩放集 CLI 示例

&#35; **创建资源组**

azure group create myrg "Southeast Asia"

&#35; **创建 VM 缩放集**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; 这将动态要求提供形参，或者可以将它们指定为实参

&#35; **获取缩放集详细信息**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; 或者获取更多详细信息：

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

即将推出的 Azure CLI 版本将包含一些强制性命令，可用于在不使用模板的情况下部署和管理 VM 缩放集，并对 VM 缩放集中的 VM 执行操作。你可以在此处跟踪此版本：[https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## VM 缩放集模板

本部分演示了一个用于创建 VM 缩放集的简单 Azure 模板，并将该模板与用于创建单实例虚拟机的模板进行比较。此外，此处还对 VM 缩放集进行了简单的视频分析：[https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### 模板剖析

让我们从一个创建由 Ubuntu 虚拟机组成的 VM 缩放集的简单模板开始，并将其分解为若干组件。此示例还创建了 VM 缩放集所依赖的资源，如 VNET 和存储帐户等。此示例的所在位置为：[https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json) - 请注意，对于此“hello world”示例，没有可用于从 VNET外部直接连接到 VM 缩放集中的 VM 的方法，因为向 VM 分配的是内部 IP 地址。请参阅“方案”部分和 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)中的示例，了解使用负载平衡器或跳转盒从外部进行连接的方法。

### 模板标头。

指定架构和内容版本：

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Parameters

与任何 Azure 资源管理器 (ARM) 模板一样，本部分定义在部署时指定的参数，在本示例中这包括 VM 缩放集的名称、开始时的 VM 实例数、创建存储帐户使用的唯一字符串（为存储帐户等对象输入值时，始终使用小写，除非你知道如何处理大小写）。

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### 变量

一个标准 ARM 模板组件，用于定义你稍后将在模板中引用的变量。在本示例中，我们将使用变量来定义想要使用的操作系统、某些网络配置详细信息，以及存储设置和存储位置。对于位置，我们将使用 _location()_ 函数来从部署该位置的资源组中进行选取。

请注意，为存储帐户前缀定义了一组唯一字符串。有关说明，请参阅“存储”部分。

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### 资源

在本部分中，对每种资源类型进行了定义。

````
   "resources": [
````


**存储。** 创建 VM 缩放集时，指定一组存储帐户。然后，将在每个帐户间平均分配 VM 实例的 OS 磁盘。以后，VM 缩放集将切换为使用托管磁盘方法，是用该方法时，无需管理存储帐户，而是只需将存储属性作为缩放集定义的一部分进行描述。现在，你将需要提前创建所需数目的帐户。我们建议你创建 5 个存储帐户，这将轻松支持缩放集中的多达 100 个 VM（当前最大数目）。

创建一组存储帐户后，这些帐户将分布在存储戳中的分区内，分配方案取决于存储帐户名称的第一个字母。因此，为了获得最佳性能，建议你在创建每个存储帐户时以字母表中的不同字母开头。可以进行手动命名，也可以像此示例中一样，使用 uniqueString() 函数提供伪随机分布：

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**虚拟网络。** 创建 VNET。请注意，你可以在同一 VNET 中具有多个缩放集以及单个 VM。

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "properties": {

        "addressSpace": {

          "addressPrefixes": [

            "[variables('addressPrefix')]"

          ]

        },

        "subnets": [

          {

            "name": "[variables('subnetName')]",

            "properties": {

              "addressPrefix": "[variables('subnetPrefix')]"

            }

          }

        ]

      }

    },
````

### virtualMachineScaleSets 资源

在许多方面，_virtualMachineScaleSets_ 资源就像 _virtualMachines_ 资源一样。它们都由 Microsoft.Compute 资源提供程序提供，并且属于相同的级别。主要的差异是你将提供一个 _capacity_ 值来表明创建的 VM 数，并且你在此处定义的内容适用于 VM 缩放集中的所有 VM。

请注意 _dependsOn_ 部分如何引用上文中创建的存储帐户和 VNET，以及 capacity 部分如何引用 _instanceCount_ 参数。

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**属性**

VM 缩放集具有 upgradePolicy 设置。将来的版本将支持切分更新（例如每次更改 VM 20% 配置），但是目前，upgradePolicy 可设置为“手动”或“自动”。“手动”意味着如果你更改了模板并重新部署，则仅当创建了新 VM 或更新了扩展时（例如，增大了 _capacity_ 时），所做的更改才会生效。“自动”意味着完全更新所有 VM，将重启所有内容。“手动”通常是更安全的方法。可以删除单独的 VM，然后根据需要重新部署以逐步更新。

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**属性->virtualMachineProfile**

此处，可将上面创建的存储帐户引用为 VM 的容器。无需预先创建实际的容器，只需创建帐户。

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.chinacloudapi.cn/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.chinacloudapi.cn/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.chinacloudapi.cn/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.chinacloudapi.cn/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.chinacloudapi.cn/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**属性->osProfile**

VM 缩放集与单独的 VM 的一个不同之处是计算机名是一个前缀。创建 VM 缩放集内的 VM 实例时，将使用以下形式的名称：myvm\_0 和 myvm\_1 等。

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**属性->networkProfile**

为 VMSS 定义网络配置文件时，请记住 NIC 配置和 IP 配置都具有一个名称。NIC 配置具有“_主_”设置，并且子网 ID 向后引用上文中作为 VNET 的一部分创建的子网资源。

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**属性->extensionProfile**

上面的简单示例不需要扩展配置文件。你可以在此模板中看到一个使用了扩展配置文件的示例，它使用 CustomScript 扩展部署 Apache 和 PHP：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) - 请注意，此示例中的网络 IP 属性还引用了负载平衡器。“VM 缩放集方案”部分中将对负载平衡器进行更详细的描述。

## VM 缩放集方案

本部分以一些典型的 VM 缩放集方案和示例模板为基础。虽然这些方案现在需要模板，但将来其中一些方案将集成到门户中。此外，某些高级 Azure 服务（如 Batch、Service Fabric、Azure 容器服务）也将使用这些方案

## 通过 RDP/SSH 连接到VM 缩放集实例

VM 是在 VNET 中创建的，并且没有为其中单独的 VM 分配公共 IP 地址。这是一件好事，因为你通常不希望承担为计算网格中的所有无状态资源分配单独的 IP 地址而产生支出和管理开销，并且你可以轻松地从 VNET 中的其他资源（包括负载平衡器或独立虚拟机等具有公共 IP 地址的资源）连接到这些 VM。

下面描述了两种连接到 VMSS VM 简单方法：

### 使用 NAT 规则连接到 VM

可以创建一个公共 IP 地址，并将其分配给负载平衡器，然后定义一个入站 NAT 规则，用于将 IP 地址上的端口映射到 VM 缩放集中的 VM 上的端口。例如

Public IP->Port 50000 -> vmss\_0->Port 22
Public IP->Port 50001 -> vmss\_1->Port 22
Public IP->Port 50002-> vmss_2->Port 22

下面的示例创建了一个 VM 缩放集，该缩放集使用 NAT 规则，通过单个公共 IP 实现与缩放集中每个 VM 的 SSH 连接：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

下面的示例使用 RDP 和 Windows 实现相同的目的：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### 使用“跳转盒”连接到 VM

如果在同一个 VNET 中创建一个 VM 缩放集和一个独立 VM，则该独立 VM 和 VMSS VM 能够使用其由 VNET/子网定义的内部 IP 地址彼此连接。

如果创建一个公共 IP 地址并将其分配给独立 VM，则可以通过 RDP 或 SSH 连接到该独立 VM，然后从该虚拟机连接到 VMSS 实例。此时你可能会发现，与使用其默认配置中的公共 IP 地址的简单独立 VM 相比，简单的 VM 缩放集本质上更安全。

作为此方法的一个示例，此模板创建了一个简单的 Mesos 群集，其中包含一个独立的主 VM，用于管理由 VM 组成的基于 VM 缩放集的群集：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## VM 缩放集实例的轮循机制负载平衡

如果你想要使用“轮循机制”方法向 VM 的计算群集交付工作，则可以使用负载平衡规则对 Azure 负载平衡器进行相应的配置。你还可以定义探测，通过使用指定的协议、间隔和请求路径对端口执行 ping 操作来验证应用程序是否正在运行。

下面的示例创建由在 IIS Web 服务器上运行的 VM 组成的 VM 缩放集，并使用负载平衡器来平衡每个 VM 接收的负载。它还使用 HTTP 协议对每个 VM 上的特定 URL 执行 ping 操作：[https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) -查看 Microsoft.Network/loadBalancers 资源类型以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile。

## 使用 Azure 自动缩放的 VM 缩放集实例

如果你想要根据 CPU/内存/磁盘使用情况或其他事件改变 VM 缩放集的实例计数 (_capacity_)，可使用 Microsoft.Insights 资源提供程序提供的一组丰富的自动缩放设置。可在 Insights REST 文档中查看有关可用设置的内容：[https://msdn.microsoft.com/zh-cn/library/azure/dn931953.aspx](https://msdn.microsoft.com/zh-cn/library/azure/dn931953.aspx)。

Insights 自动缩放与 VM 缩放集直接集成。若要进行设置，请定义 Microsoft.Insights/autoscaleSettings 资源类型，此类型具有向后引用缩放集的 _targetResourceUri_ 和 _metricResourceUri_。定义缩放集时，请包括 _extensionProfile_，用于在 Windows 上安装 Azure 诊断代理 (WAD) 或在 Linux 上安装 Linux 诊断扩展 (LDE)。下面是一个 Linux 示例，该示例在平均 CPU 使用率 > 50% 的情况下，在 5 分钟的采样期间内，以 1 分钟的时间粒度添加 VM，直至达到预定义限制：

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)。

以后，VM 缩放集的自动缩放也将直接与 Azure 门户集成。

## 在 PaaS 群集管理器中将 VM 缩放集部署为计算群集

有时会将 VM 缩放集描述为下一代的辅助角色。这是有效的描述，但也可能导致将缩放集功能与 PaaS v1 辅助角色功能混淆。

在某种意义上，VM 缩放集提供真正的“辅助角色”或辅助角色资源，并在此资源中提供独立于平台/运行时且集成到 Azure 资源管理器 IaaS 中的可自定义通用计算资源。

PaaS v1 辅助角色虽然在平台/运行时支持方面受到限制（仅 Windows 平台映像），但它也包括多项服务，如 VIP 交换，可配置升级设置，以及_尚未_在 VM 缩放集中提供，或者将由 Service Fabric 等其他更高级别 PaaS 服务提供的特定于运行时/应用部署的设置。考虑到这一点，你可以将 VM 缩放集视为支持 PaaS 的基础结构。即，生成 Service Fabric 等 PaaS 解决方案或 Mesos 等群集管理器时，可以在将 VM 缩放集作为可缩放计算层的基础上进行生成。

下面的示例演示 Mesos 群集，它在同一 VNET 中将 VM 缩放集部署为一个独立 VM。独立 VM 是一个 Mesos 主机，而 VM 缩放集表示一组从属节点：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。[Azure 容器服务](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/)的将来版本将基于 VM 缩放集部署更多复杂/强化版本。

## 扩大和缩小 VM

要增加或减少 VM 缩放集中的虚拟机数目，只需更改 _capacity_ 属性并重新部署模板。这种简单性可让你在想要定义 Azure 自动缩放不支持的自定义缩放事件时轻松编写自己的自定义缩放层。

如果要重新部署模板以更改容量，则可以定义只包括 SKU 和已更新容量的更小模板。以下是一个相关示例：[https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)。

## VM 缩放集性能和缩放指南

- 在公共预览期间，不要一次在多个 VM 缩放集中创建超过 500 个 VM。将来会增加此限制。
- 为每个存储帐户规划不超过 40 个 VM。
- 使存储帐户名称的第一个字母尽可能的彼此不同。[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)中的示例 VMSS 模板举例说明如何执行此操作。
- 如果使用自定义 VM，请为单个存储帐户中的每个 VM 缩放集规划不超过 40 VM。你将需要预先将映像复制到存储帐户，然后才能开始进行 VM 缩放集部署。有关详细信息，请参阅 FAQ。
- 为每个 VNET 规划不超过 2048 个 VM。将来会增加此限制。
- 可创建的 VM 数目受到任何区域中计算核心配额的限制。即使你对用于云服务或 IaaS v1 的核心已具有较高的限制，也仍可能需要联系客户支持才可增加计算配额限制。要查询你的配额，可运行以下 Azure CLI 命令：_azure vm list-usage_，和以下 PowerShell 命令：_Get-AzureRmVMUsage_（如果使用的 PowerShell 版本低于 1.0，则使用 _Get-AzureVMUsage_）。


## VM 缩放集 FAQ

**问：VM 缩放集中可以有多少台 VM？**

A.如果使用可分布在多个存储帐户中的平台映像，则可以有 100 台。如果使用自定义映像，则最多可以有 40 台，因为在预览期间，自定义映像限于单个存储帐户。

**对于 VM 缩放集还存在哪些其他资源限制？**

A.现有的 Azure 服务限制适用：[/documentation/articles/azure-subscription-service-limits/](/documentation/articles/azure-subscription-service-limits/)

在预览期间，你还仅可在每个区域的多个缩放集中创建不超过 500 台 VM。

**问：VM 缩放集内是否支持数据磁盘？**

A.在首次发行时不支持。可用于存储数据的选项包括：

- Azure 文件（SMB 共享驱动器）

- OS 驱动器

- 临时驱动器（本地，不受 Azure 存储的支持）

- 外部数据服务（如远程 DB、Azure 表、Azure blob）

**问：哪些 Azure 区域支持 VMSS？**

A.支持 Azure 资源管理器的任何区域均支持 VM 缩放集。

**问：如何使用自定义映像创建 VMSS？**

A.将 vhdContainers 属性留空，例如：

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.chinacloudapi.cn/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.chinacloudapi.cn/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**问：如果我将 VMSS 容量从 20 减少到 15，将删除哪些 VM？**

A.将删除最后（索引最大的）5 台 VM。

**问：如果我再将容量从 15 增加到 18，又将发生什么情况？**

如果将容量增加到 18，将创建索引为 15、16 和 17 的 VM。在这两种情况下，FD 与 UD 中的 VM 都是平衡的。

**问：在一个 VM 缩放集中使用多个扩展时，是否可以强制规定一个执行序列？**

A.不能直接规定，但对于 customScript 扩展，你的脚本可以等待另一个扩展完成（例如，通过监视扩展日志 - 请参阅 [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)）。

**问：VM 缩放集展是否可与 Azure 可用性集配合使用？**

A.是的。缩放集是含有 3 个 FD 和 5 个 UD 的隐式可用性集。无需在 virtualMachineProfile 下进行任何配置。在将来的版本中，缩放集可能跨多个租户，但目前一个缩放集只是单个可用性集。

## 后续步骤

在 VM 缩放集的预览期间，文档功能将不断发展，并且将开发门户和自动缩放等更多集成功能。

你的反馈非常重要，可帮助我们构建满足你的需求的服务。请让我们知道有用的内容、无用的内容和需要改进的内容。你可将反馈发送到 [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com)。

## 资源

| **主题** | **链接** |
| --- | --- |
| Azure 资源管理器概述 | [/documentation/articles/resource-group-overview/](/documentation/articles/resource-group-overview/) |
| Azure 资源管理器模板 | [/documentation/articles/resource-group-authoring-templates/](/documentation/articles/resource-group-authoring-templates/) |
| Azure 资源管理器模板函数 | [/documentation/articles/resource-group-template-functions/](/documentation/articles/resource-group-template-functions/) |
| 示例模板 (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| VM 缩放集 REST API 指南 | [https://msdn.microsoft.com/zh-cn/library/mt589023.aspx](https://msdn.microsoft.com/zh-cn/library/mt589023.aspx) |
| VM 缩放集视频 | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| 自动缩放设置 (MSDN) | [https://msdn.microsoft.com/zh-cn/library/azure/dn931953.aspx](https://msdn.microsoft.com/zh-cn/library/azure/dn931953.aspx) |
| Azure 中的资源组部署故障排除 | [/documentation/articles/resource-group-deploy-debug/](/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Mooncake_1207_2015-->