---
title: "在 Azure 规模集模板中引用自定义映像 | Microsoft Docs"
description: "了解如何向现有 Azure 虚拟机规模集模板添加自定义映像"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>向 Azure 规模集模板添加自定义映像

本文介绍如何修改[最小可行规模集模板](./virtual-machine-scale-sets-mvss-start.md) 以通过自定义映像部署。

## <a name="change-the-template-definition"></a>更改模板定义

可在[此处](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)查看最小可行规模集模板，可在[此处](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json)查看用于从自定义映像部署规模集的模板。 让我们逐一查看创建此模板 (`git diff minimum-viable-scale-set custom-image`) 时使用的差异内容：

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
+      "apiVersion": "2016-04-30-preview",
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
       "apiVersion": "2016-04-30-preview",
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

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

本示例中，使用 `resourceId` 函数获取在同一模板中创建的映像的资源 ID。 如果事先创建了托管磁盘映像，应改为提供该映像的 ID。 此 ID 必须采用以下格式：`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`。


## <a name="next-steps"></a>后续步骤

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
