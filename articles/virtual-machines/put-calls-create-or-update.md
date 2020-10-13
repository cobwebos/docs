---
title: 用于创建或更新操作的 PUT 调用
description: 对计算资源进行创建或更新操作的调用
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978554"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>放置用于计算资源的创建或更新的调用

`Microsoft.Compute` 资源不支持 *HTTP PUT* 语义的常规定义。 相反，这些资源为 PUT 和 PATCH 谓词使用修补语义。

**创建** 操作在适当时应用默认值。 但是，通过 PUT 或 PATCH 完成的资源 **更新** 不会应用任何默认属性。 **更新** 操作应用应用严格修补语义。

例如， `caching` `ReadWrite` 如果资源是 OS 磁盘，则虚拟机的磁盘属性默认为。

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

但是，在属性被省略或传递*null*值时，对于**更新**操作，它将保持不变，并且没有默认值。

将更新操作发送到资源时，这一点非常重要，目的是删除关联。 如果该资源是 `Microsoft.Compute` 资源，则需要显式调用要删除的对应属性，并为其分配值。 为实现此目的，用户可以传递空字符串（如 **""**）。 这将指示平台删除该关联。

> [!IMPORTANT]
> 不支持 "修补" 数组元素。 相反，客户端必须执行 PUT 或 PATCH 请求，其中包含已更新数组的全部内容。 例如，若要将数据磁盘从 VM 分离，请执行 GET 请求以获取当前 VM 模型，删除要从其分离的磁盘， `properties.storageProfile.dataDisks` 并使用更新的 VM 实体执行 PUT 请求。

## <a name="examples"></a>示例

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>用于删除邻近位置组关联的正确负载

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>用于删除邻近位置组关联的错误负载不正确

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>后续步骤
了解有关创建或更新[虚拟机](/rest/api/compute/virtualmachines/createorupdate)和[虚拟机规模集](/rest/api/compute/virtualmachinescalesets/createorupdate)的调用的详细信息