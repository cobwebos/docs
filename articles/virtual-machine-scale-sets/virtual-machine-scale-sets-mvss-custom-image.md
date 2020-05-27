---
title: 在 Azure 规模集模板中引用自定义映像
description: 了解如何向现有 Azure 虚拟机规模集模板添加自定义映像
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/26/2018
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5ed9ee79dde73e738417031b928a675ea913179c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124901"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>向 Azure 规模集模板添加自定义映像

本文介绍如何修改[基本规模集模板](virtual-machine-scale-sets-mvss-start.md)以通过自定义映像进行部署。

## <a name="change-the-template-definition"></a>更改模板定义
在[前面的文章](virtual-machine-scale-sets-mvss-start.md)中，我们创建了一个基本规模集模板。 现在，我们将使用前面的模板并对其进行修改，以创建一个模板，该模板通过自定义映像部署规模集。  

### <a name="creating-a-managed-disk-image"></a>创建托管磁盘映像

如果已有自定义托管磁盘映像（类型为 `Microsoft.Compute/images` 的资源），可跳过此部分。

首先添加 `sourceImageVhdUri` 参数，它是 Azure 存储中通用 Blob 的 URI，包含要从其部署的自定义映像。


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

接下来，添加类型为 `Microsoft.Compute/images` 的资源，该资源是托管磁盘映像，基于 URI `sourceImageVhdUri` 处的通用 Blob。 该映像必须与使用它的规模集位于同一区域。 在映像属性中，指定操作系统类型、Blob 位置（通过 `sourceImageVhdUri` 参数）和存储帐户类型：

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

在规模集资源中，添加引用自定义映像的 `dependsOn` 子句，确保在规模集尝试从该映像部署之前创建映像：

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>更改规模集属性以使用托管磁盘映像

在规模集 `storageProfile` 的 `imageReference` 中，请勿指定平台映像的发布者、产品/服务、SKU 和版本，而是指定 `Microsoft.Compute/images` 资源的 `id`：

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

本示例中，使用 `resourceId` 函数获取在同一模板中创建的映像的资源 ID。 如果事先创建了托管磁盘映像，应改为提供该映像的 ID。 此 ID 必须采用以下格式：`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`。


## <a name="next-steps"></a>后续步骤

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
