---
title: 浏览 Azure 资源
description: 了解如何使用 Resource Graph 查询语言浏览资源并发现资源的连接方式。
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 33bf457a57f7e62b9c99471bcb7676f62046f61d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654489"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>使用 Resource Graph 浏览 Azure 资源

Azure Resource Graph 提供快速、大规模浏览和发现 Azure 资源的功能。 它专为快速响应而设计，是了解你的环境以及构成 Azure 资源的属性的好方法。

## <a name="explore-virtual-machines"></a>浏览虚拟机

Azure 中的一种常见资源是虚拟机。 作为资源类型，虚拟机具有许多可以查询的属性。 每个属性都提供了一个筛选或查找你正在寻找的资源的选项。

### <a name="virtual-machine-discovery"></a>虚拟机发现

让我们从一个简单的查询开始，从环境中获取一个 VM 并查看返回的属性。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` cmdlet 默认情况下会返回 PSCustomObject  。 若要让输出与 Azure CLI 返回的内容相同，请使用 `ConvertTo-Json` cmdlet。 “深度”  的默认值是“2”  。 将它设置为“100”  应转换所有返回的级别。

JSON 结果的结构类似于下面的示例：

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

属性告诉我们有关虚拟机资源本身的其他信息，包括 SKU、OS、磁盘、标记以及它所属的资源组和订阅信息。

### <a name="virtual-machines-by-location"></a>按位置列出的虚拟机

根据我们对虚拟机资源的了解，我们使用“位置”  属性按位置计算所有虚拟机。 要更新查询，我们将删除限制并汇总位置值的计数。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON 结果的结构类似于下面的示例：

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

现在可以看到每个 Azure 区域中有多少个虚拟机。

### <a name="virtual-machines-by-sku"></a>按 SKU 列出的虚拟机

回到原始虚拟机属性，尝试查找 SKU 大小为“Standard_B2s”  的所有虚拟机。 查看返回的 JSON，我们看到它存储在 properties.hardwareprofile.vmsize  中。 我们将更新查询以查找与此大小匹配的所有 VM，并仅返回 VM 和区域的名称。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>连接到高级托管磁盘的虚拟机

如果要获取附加到这些 **Standard_B2s** 虚拟机的高级托管磁盘的详细信息，可以扩展查询以提供这些托管磁盘的资源 ID。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> 获得 SKU 的另一种方法是使用 aliases  属性 Microsoft.Compute/virtualMachines/sku.name  。 请参阅[显示别名](../samples/starter.md#show-aliases)和[显示不同的别名值](../samples/starter.md#distinct-alias-values)示例。

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

结果是磁盘 ID 列表。

### <a name="managed-disk-discovery"></a>托管磁盘发现

使用从上一个查询获取的第一条记录，我们将浏览已附加到第一个虚拟机的托管磁盘上存在的属性。 更新的查询使用磁盘 ID 并更改类型。

上一个查询的示例输出如下：

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

在运行查询之前，如何知道“类型”  现在是 Microsoft.Compute/disks  ？
如果查看完整 ID，会看到作为字符串一部分的 /providers/Microsoft.Compute/disks/  。 此字符串片段为你提供了要搜索的类型的提示。 另一种方法是按类型删除限制，而只搜索 ID 字段。 由于 ID 是唯一的，因此只返回一条记录，并且 ID 的 type  属性提供该详细信息。

> [!NOTE]
> 要使此示例起作用，必须使用自己的环境中的结果替换 ID 字段。

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON 结果的结构类似于下面的示例：

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>浏览虚拟机以查找公共 IP 地址

这一组查询首先查找并存储已连接到虚拟机的所有网络接口 (NIC) 资源。 然后，查询使用 NIC 列表查找是公共 IP 地址的每个 IP 地址资源并存储这些值。 最后，查询提供公共 IP 地址的列表。

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

在下一个查询中使用文件 (Azure CLI) 或变量 (Azure PowerShell) 获取 NIC 附加了公共 IP 地址的相关网络接口资源详细信息。

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

最后，使用存储在文件 (Azure CLI) 或变量 (Azure PowerShell) 中的公共 IP 地址资源列表从相关对象获取实际公共 IP 地址并显示。

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

若要了解如何使用 `join` 运算符在单个查询中完成这些步骤，请参阅[列出虚拟机及其网络接口和公共 IP](../samples/advanced.md#join-vmpip) 示例。

## <a name="next-steps"></a>后续步骤

- 详细了解[查询语言](query-language.md)。
- 在[初学者查询](../samples/starter.md)中了解使用的语言。
- 在[高级查询](../samples/advanced.md)中了解高级用法。
