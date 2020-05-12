---
title: 了解虚拟机规模集模板
description: 了解如何通过几个简单的步骤为 Azure 虚拟机规模集创建基本规模集模板。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: af2f000b9f9a7bf64898c46b3126cf180802b445
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198125"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>了解虚拟机规模集模板
[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process)是部署成组的相关资源的好办法。 本系列教程演示如何创建基本规模集模板，以及如何修改此模板以满足各种场景。 所有示例都来自此 [GitHub 存储库](https://github.com/gatneil/mvss)。

此模板简单易用。 有关更完整的规模集模板的示例，请参阅 [Azure 快速入门模板 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)，并搜索包含字符串 `vmss` 的文件夹。

如果已熟悉模板创建，则可以跳到“后续步骤”部分，查看如何修改此模板。

## <a name="define-schema-and-contentversion"></a>定义 $schema 和 contentVersion
首先，定义模板中的 `$schema` 和 `contentVersion`。 `$schema` 元素定义模板语言的版本，并用于 Visual Studio 语法突出显示和类似的验证功能。 `contentVersion` 元素不由 Azure 使用。 而是帮助跟踪模板版本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>定义参数
接下来，定义两个参数：`adminUsername` 和 `adminPassword`。 参数是部署时指定的值。 `adminUsername` 参数只是一个 `string` 类型，但是由于 `adminPassword` 是一个机密，因此将其类型指定为 `securestring`。 稍后，这些参数会被传递到规模集配置。

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>定义变量
Resource Manager 模板还可用于定义以后要在模板中使用的变量。 本示例不使用任何变量，因此 JSON 对象为空。

```json
  "variables": {},
```

## <a name="define-resources"></a>定义资源
接下来是模板的资源部分。 在此处定义实际需要部署的内容。 与 `parameters` 和 `variables`（它们是 JSON 对象）不同，`resources` 是 JSON 对象的 JSON 列表。

```json
   "resources": [
```

所有资源都需要 `type`、`name`、`apiVersion` 和 `location` 属性。 此示例的第一个资源具有类型 [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks)、名称 `myVnet` 和 apiVersion `2018-11-01`。 （若要查找资源类型的最新 API 版本，请参阅 [Azure 资源管理器模板参考](/azure/templates/)。）

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>指定位置
若要指定虚拟网络的位置，请使用[资源管理器模板函数](../azure-resource-manager/templates/template-functions.md)。 此函数必须括在引号和方括号内，如：`"[<template-function>]"`。 在本例中，使用 `resourceGroup` 函数。 该函数不使用任何参数，并返回 JSON 对象和有关要将部署部署到的资源组的元数据。 资源组在部署时由用户进行设置。 然后此值会通过 `.location` 编入到该 JSON 对象的索引中，以便从该 JSON 对象中获取位置。

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>指定虚拟网络属性
每个 Resource Manager 资源都有自己的针对特定于的资源的配置的 `properties` 部分。 在本示例中，使用专用 IP 地址范围 `10.0.0.0/16` 指定虚拟网络应具有一个子网。 规模集始终包含在一个子网中。 它不能跨子网。

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>添加 dependsOn 列表
除所需的 `type`、`name`、`apiVersion` 和 `location` 属性外，每个资源还可具有可选的字符串 `dependsOn` 列表。 此列表指定部署此资源前，必须先部署哪些其他资源。

在本示例中，列表中只有一个元素，即前面示例中的虚拟网络。 指定此依赖项的原因是在创建任何 VM 之前规模集需要有网络。 这样，规模集可为这些虚拟机提供之前在网络属性中指定的 IP 地址范围中的专用 IP 地址。 dependsOn 列表中每个字符串的格式为 `<type>/<name>`。 使用先前虚拟机资源定义中使用的同一 `type` 和 `name`。

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>指定规模集属性
规模集具有多个用于自定义规模集中 VM 的属性。 有关这些属性的完整列表，请参阅[模板参考](/azure/templates/microsoft.compute/virtualmachinescalesets)。 在本教程中，仅设置一些常用属性。
### <a name="supply-vm-size-and-capacity"></a>提供 VM 大小和容量
规模集需要知道要创建的 VM 的大小（“sku name”） 和要创建的此类 VM的 数量（“sku capacity”）。 若要查看可用的 VM 大小，请参阅 [VM 大小文档](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)。

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>选择更新类型
规模集还需要知道如何处理规模集的更新。 目前有三个选项：`Manual`、`Rolling`、`Automatic`。 有关这两者之间的区别的详细信息，请参阅文档[如何升级规模集](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)。

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>选择 VM 操作系统
规模集需知道要在 VM 上安装什么操作系统。 此处使用完全修补的 Ubuntu 16.04-LTS 映像创建 VM。

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>指定 computerNamePrefix
此规模集部署多个 VM。 指定 `computerNamePrefix` 而不是指定每个 VM 名称。 规模集在每个 VM 的前缀中附加一个索引，因此 VM 名称格式为 `<computerNamePrefix>_<auto-generated-index>`。

在以下代码片段中，使用前面提到的参数为规模集中所有 VM 设置管理员用户名和密码。 此过程使用 `parameters` 模板函数。 此函数采用一个字符串，此字符串指定要引用哪个参数及哪个参数输出该参数的值。

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>指定 VM 网络配置
最后，指定规模集中 VM 的网络配置。 在本例中，仅需指定之前创建的子网的 ID。 此会告知规模集将网络接口置于此子网中。

可使用 `resourceId` 模板函数获取包含子网的虚拟网络的 ID。 此函数采用资源的类型和名称，并返回该资源的完全限定的标识符。 此 ID 格式为：`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

但是，仅有虚拟网络的标识符是不够的。 提供规模集 VM 应位于的特定子网。 为此，请将 `/subnets/mySubnet` 串联到虚拟网络的 ID。 其结果是子网的完全限定的 ID。 使用 `concat` 函数执行此串联，该函数使用一系列字符串并返回它们的串联字符串。

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
