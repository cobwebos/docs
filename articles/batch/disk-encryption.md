---
title: 在启用磁盘加密的情况下创建池
description: 了解如何使用磁盘加密配置来通过平台管理的密钥来加密节点。
author: pkshultz
ms.topic: how-to
ms.date: 08/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 9b0f7f9963ee0edd3986f7ec808a8a4060d857f8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267032"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>在启用磁盘加密的情况下创建池

使用虚拟机配置创建 Azure Batch 池时，可以通过指定磁盘加密配置，使用平台托管密钥来加密池中的计算节点。

本文介绍如何创建启用了磁盘加密的批处理池。

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>为什么要使用具有磁盘加密配置的池？

使用批处理池，可以访问和存储计算节点的 OS 和临时磁盘上的数据。 使用平台托管的密钥对服务器端磁盘进行加密将以较低的开销和便利性来保护这些数据。  

根据池配置和区域可支持性，批处理将在计算节点上应用其中一种磁盘加密技术。

- [托管磁盘加密和平台托管密钥](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [使用平台托管密钥在主机上加密](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

> [!IMPORTANT]
> 在 Azure Batch 中使用平台托管密钥的主机上的加密支持目前以公共预览版提供，美国东部、美国西部2、美国中南部、US Gov 弗吉尼亚州和 US Gov 亚利桑那州区域。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你无法指定将哪个加密方法应用于池中的节点。 相反，你需要提供要在其节点上加密的目标磁盘，Batch 可以选择适当的加密方法，以确保在计算节点上对指定的磁盘进行加密。
 
## <a name="azure-portal"></a>Azure 门户 

在 Azure 门户中创建 Batch 池时，请在 "**磁盘加密配置**" 下选择 " **TemporaryDisk** " 或 " **OsAndTemporaryDisk** "。

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Azure 门户中的磁盘加密配置选项的屏幕截图。":::

创建池后，可在池的 " **属性** " 部分中查看磁盘加密配置目标。

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="显示 Azure 门户中的磁盘加密配置目标的屏幕截图。":::

## <a name="examples"></a>示例

以下示例演示了如何使用 Batch .NET SDK、批处理 REST API 和 Azure CLI 来加密批处理池中的 OS 和临时磁盘。

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL：
```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```
请求正文：
```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 磁盘存储的服务器端加密](../virtual-machines/windows/disk-encryption.md)。
- 有关 Batch 的详细概述，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。
