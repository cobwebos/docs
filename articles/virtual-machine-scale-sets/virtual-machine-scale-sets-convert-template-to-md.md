---
title: 将规模集模板转换为使用托管磁盘
description: 将 Azure 资源管理器虚拟机规模集模板转换为托管磁盘规模集模板。
keywords: 虚拟机规模集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 5/18/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 85f8694a017c8de94d987c244994a24ad0929441
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124884"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>将规模集模板转换为托管磁盘规模集模板

使用 Resource Manager 模板创建不使用托管磁盘的规模集的客户可能希望修改该模板以使用托管磁盘。 本文演示如何使用托管磁盘，以 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)（适用于示例资源管理器模板的社区主导存储库）中的拉取请求为例。 可在此处查看完整的拉取请求：[https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)，差异的相关部分如下，并进行了说明：

## <a name="making-the-os-disks-managed"></a>将 OS 磁盘设为托管磁盘

在以下差异部分中，已删除与存储帐户和磁盘属性相关的多个变量。 不再需要存储帐户类型（Standard_LRS 是默认值），但可以指定它（如果需要）。 托管磁盘仅支持 Standard_LRS 和 Premium_LRS。 旧模板中使用了新存储帐户后缀、唯一的字符串数组和 sa 计数来生成存储帐户名称。 在新模板中这些变量不再需要，因为托管磁盘会自动以客户的名义创建存储帐户。 同样，vhd 容器名称和 OS 磁盘名称也不再需要，因为托管磁盘会自动命名基础存储 blob 容器和磁盘。

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


在以下差异部分中，已将计算 API 更新到版本 2016-04-30-preview，该版本是支持带规模集的托管磁盘所需的最早版本。 可以在使用旧语法的新 API 版本中使用非托管磁盘（如果需要）。 如果只更新计算 API 版本，而不更改任何其他内容，则模板应像以前那样继续工作。

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

在以下差异部分中，已将存储帐户资源从资源数组中完全删除。 不再需要该资源，因为托管磁盘会自动创建它们。

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

在以下差异部分中，我们可以看到，我们要删除从规模集引用创建存储帐户的循环的 depends on 子句。 在旧模板中，此子句可确保开始创建规模集之前已创建存储帐户，但对于托管磁盘，此子句不再需要。 还会删除 vhd 容器属性和 OS 磁盘名称属性，因为托管磁盘会在后台自动处理这些属性。 如果想要高级 OS 磁盘，可以在“osDisk”配置中添加 `"managedDisk": { "storageAccountType": "Premium_LRS" }`。 只有 VM sku 中带大写或小写“s”的 VM 可以使用高级磁盘。

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

规模集配置中没有显式属性可设置是使用托管磁盘还是使用非托管磁盘。 规模集根据存储配置文件中提供的属性，知道使用哪种磁盘。 因此，修改模板时请务必确保相应属性存在于规模集的存储配置文件中。


## <a name="data-disks"></a>数据磁盘数

进行上述更改后，规模集将使用托管磁盘作为 OS 磁盘，但数据磁盘怎么办？ 若要添加数据磁盘，请在与“osDisk”同一级别的“storageProfile”下添加“dataDisks”属性。 该属性的值是 JSON 对象列表，其中每个对象均具有属性“lun”（必须对于 VM 上的每个数据磁盘唯一）、“createOption”（“empty”是当前唯一支持的选项）和“diskSizeGB”（磁盘的大小，以 GB 为单位；必须大于 0 且小于 1024）， 如以下示例所示：

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

如果在此数组中指定 `n` 个磁盘，则规模集中的每个 VM 会获得 `n` 个数据磁盘。 但是，请注意，这些数据磁盘是原始设备。 它们未进行格式化。 在使用这些磁盘之前，由客户负责附加它们并对其进行分区和格式化。 （可选）还可以在每个数据磁盘对象中指定 `"managedDisk": { "storageAccountType": "Premium_LRS" }`，以指定它应是高级数据磁盘。 只有 VM sku 中带大写或小写“s”的 VM 可以使用高级磁盘。

若要详细了解如何在规模集中使用数据磁盘，请参阅[此文](./virtual-machine-scale-sets-attached-disks.md)。


## <a name="next-steps"></a>后续步骤
例如资源管理器使用规模集的模板，请在[Azure 快速入门模板 GitHub](https://github.com/Azure/azure-quickstart-templates)存储库中搜索 "vmss"。

有关一般信息，请参阅[规模集的主要登陆页](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

