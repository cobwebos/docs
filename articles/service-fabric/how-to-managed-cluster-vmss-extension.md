---
title: " (预览版，将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型) "
description: 下面介绍如何将虚拟机规模集扩展添加 Service Fabric 管理群集节点类型
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410295"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a> (预览版，将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型) 

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 这使你能够将 [虚拟机规模集扩展](../virtual-machines/extensions/overview.md) 添加到 Service Fabric 托管群集节点类型。

可以使用 [AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true) PowerShell 命令将虚拟机规模集扩展添加到节点类型。

或者，你可以在 Azure 资源管理器模板的 Service Fabric 托管群集节点类型上使用虚拟机规模集扩展，例如：

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

有关配置 Service Fabric 托管群集节点类型的详细信息，请参阅 [托管群集节点类型](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Service Fabric 托管群集的详细信息，请参阅：

> [!div class="nextstepaction"]
> [Service Fabric 托管群集常见问题解答](./faq-managed-cluster.md)
