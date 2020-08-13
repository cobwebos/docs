---
title: Azure 实例元数据服务
description: 一个 RESTful 接口，用于获取有关 VM 计算、网络和即将发生的维护事件的信息。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: fe059f684306e2c98e625af72248f03f0932ebad
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168263"
---
# <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

Azure 实例元数据服务 (IMDS) 提供有关当前正在运行的虚拟机实例的信息，可用于管理和配置虚拟机。
这些信息包括 SKU、存储、网络配置和即将发生的维护事件。 有关提供的数据的完整列表，请参阅[元数据 API](#metadata-apis)。
实例元数据服务适用于 VM 和虚拟机规模集实例。 它仅可用于运行使用 [Azure 资源管理器](/rest/api/resources/)创建/管理的 VM。

Azure 的 IMDS 是一个 REST 终结点，位于已知不可路由的 IP 地址 (`169.254.169.254`)，只能从 VM 中访问。 VM 与 IMDS 之间的通信绝不会离开主机。
最佳做法是让 HTTP 客户端在查询 IMDS 时绕过 VM 中的 web 代理并同等对待 `169.254.169.254` 和 [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="security"></a>安全性

此实例元数据服务终结点只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问。 此外，任何包含`X-Forwarded-For`标头的请求都会被服务拒绝。
请求必须包含 `Metadata: true` 标头，以确保实际请求是直接计划好的，而不是无意重定向的一部分。

> [!IMPORTANT]
> 实例元数据服务不是用于敏感数据的通道。 该终结点面向 VM 上的所有进程开放。 应将通过此服务公开的信息视为与 VM 内运行的所有应用程序共享的信息。

## <a name="usage"></a>使用情况

### <a name="accessing-azure-instance-metadata-service"></a>访问 Azure 实例元数据服务

若要访问实例元数据服务，请从 [Azure 资源管理器](/rest/api/resources/)或 [Azure 门户](https://portal.azure.com)创建一个 VM，并按照以下示例操作。
有关如何查询 IMDS 的更多示例，请参阅 [Azure 实例元数据示例](https://github.com/microsoft/azureimds)。

下面是用于检索实例的所有元数据的示例代码。若要访问特定数据源，请参阅[元数据 API](#metadata-apis) 部分。 

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-06-01
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>数据输出


默认情况下，实例元数据服务会返回 JSON 格式的数据 (`Content-Type: application/json`)。 但是，某些 API 可以返回不同格式的数据（如果请求）。
下表是有关 API 可支持的其他数据格式的参考。

API | 默认数据格式 | 其他格式
--------|---------------------|--------------
/attested | json | 无
/identity | json | 无
/instance | json | text
/scheduledevents | json | none

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 对于 /metadata/instance 中的叶节点，`format=json` 不起作用。 对于这些查询，需要显式指定 `format=text`，因为默认格式为 json。

### <a name="versioning"></a>版本控制

实例元数据服务进行了版本控制，因此，必须在 HTTP 请求中指定 API 版本。

支持的 API 版本如下： 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01

请注意，新版本发布后，需要一段时间才能推广到所有区域。

在添加更新的版本时，早期版本仍可供访问以保持兼容性（如果脚本依赖于特定的数据格式）。

如果未指定版本，则会返回错误并列出受支持的最新版本。

> [!NOTE]
> 此响应是 JSON 字符串。 下例说明了未指定版本时出现的错误情况，为了便于阅读，响应显示非常清晰。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**响应**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>元数据 API

元数据服务包含多个 API（表示不同数据源）。

API | 说明 | 引入的版本
----|-------------|-----------------------
/attested | 请参阅[证明数据](#attested-data) | 2018-10-01
/identity | 请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | 请参阅[实例 API](#instance-api) | 2017-04-02
/scheduledevents | 请参阅[计划事件](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>实例 API

实例 API 公开 VM 实例的重要元数据，其中包括 VM、网络和存储。 可以通过实例/计算访问以下类别：

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
azEnvironment | VM 运行时所在的 Azure 环境 | 2018-10-01
customData | 此功能目前已禁用。 当该功能可用时，我们将更新此文档 | 2019-02-01
isHostCompatibilityLayerVm | 标识 VM 是否在主机兼容性层上运行 | 2020-06-01
location | VM 在其中运行的 Azure 区域 | 2017-04-02
name | VM 的名称 | 2017-04-02
offer | 提供 VM 映像的信息，仅适用于从 Azure 映像库部署的映像 | 2017-04-02
osType | Linux 或 Windows | 2017-04-02
placementGroupId | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
计划 | 包含 VM 的名称、产品和发布者（如果是 Azure 市场映像）的[计划](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
platformUpdateDomain |  正在运行 VM 的[更新域](manage-availability.md) | 2017-04-02
platformFaultDomain | 正在运行 VM 的[容错域](manage-availability.md) | 2017-04-02
provider | VM 的提供商 | 2018-10-01
publicKeys | [公钥的集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)，已分配给 VM 和路径 | 2018-04-02
publisher | VM 映像的发布者 | 2017-04-02
resourceGroupName | 虚拟机的[资源组](../../azure-resource-manager/management/overview.md) | 2017-08-01
ResourceId | 资源的[完全限定](/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | VM 映像的特定 SKU | 2017-04-02
securityProfile. secureBootEnabled | 标识是否在 VM 上启用了 UEFI 安全启动 | 2020-06-01
securityProfile.virtualTpmEnabled | 确定虚拟受信任的平台模块 (是否在 VM 上启用了 TPM)  | 2020-06-01
storageProfile | 参阅[存储配置文件](#storage-metadata) | 2019-06-01
subscriptionId | 虚拟机的 Azure 订阅 | 2017-08-01
标记 | 虚拟机的[标记](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | 格式化为 JSON 数组以方便编程分析的标记  | 2019-06-04
版本 | VM 映像的版本 | 2017-04-02
vmId | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | 虚拟机规模集的[虚拟机规模集名称](../../virtual-machine-scale-sets/overview.md) | 2017-12-01
vmSize | [VM 大小](../sizes.md) | 2017-04-02
区域 | 虚拟机的[可用性区域](../../availability-zones/az-overview.md) | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>示例 1：跟踪 Azure 上正在运行的 VM

作为服务提供商，可能需要跟踪运行软件的 VM 数目，或者代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用实例元数据服务中的 `vmId` 字段。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>示例 2：基于容错/更新域放置容器、数据分区

对于某些方案，不同数据副本的放置至关重要。 例如，对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或通过 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 实现容器放置时，需要知道正在运行 VM 的 `platformFaultDomain` 和 `platformUpdateDomain`。
你还可以使用实例的[可用性区域](../../availability-zones/az-overview.md)来做出这些决策。
可直接通过实例元数据服务查询此数据。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**响应**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>示例 3：在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会接到支持电话，了解有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以支持专业人员了解有关 Azure 上的 VM 类型。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>示例 4：获取 VM 所在的 Azure 环境

Azure 具有各种主权云，如 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)。 有时你需要使用 Azure 环境来做出一些运行时决策。 以下示例显示了如何实现此行为。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**响应**

```text
AzurePublicCloud
```

Azure 环境的云和值列在下面。

 云   | Azure 环境
---------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure 政府](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>网络元数据 

网络元数据是实例 API 的一部分。 可通过实例/网络终结点获取以下网络类别。

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本地 IPv4 地址 | 2017-04-02
ipv4/publicIpAddress | VM 的公共 IPv4 地址 | 2017-04-02
subnet/address | VM 的子网地址 | 2017-04-02
subnet/prefix | 子网前缀，例如 24 | 2017-04-02
ipv6/ipAddress | VM 的本地 IPv6 地址 | 2017-04-02
macAddress | VM mac 地址 | 2017-04-02

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下所有示例响应显示清晰，可供阅读。

#### <a name="sample-1-retrieving-network-information"></a>示例 1：检索网络信息

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieving-public-ip-address"></a>示例 2：检索公共 IP 地址

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>存储元数据

存储元数据是实例/计算/storageProfile 终结点下实例 API 的一部分。
它提供与 VM 关联的存储磁盘的详细信息。 

VM 的存储配置文件分为三个类别：映像引用、OS 磁盘和数据磁盘。

映像引用对象包含有关 OS 映像的以下信息：

数据    | 说明
--------|-----------------
id      | 资源 ID
offer   | 平台或市场映像的套餐
publisher | 映像发布者
sku     | 映像 SKU
版本 | 平台或市场映像的版本

OS 磁盘对象包含有关 VM 所用 OS 磁盘的以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
image   | 源用户映像虚拟硬盘
lun     | 磁盘的逻辑单元号
managedDisk | 托管磁盘参数
name    | 磁盘名称
vhd     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 writeAccelerator

数据磁盘阵列包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象包含以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
encryptionSettings | 磁盘的加密设置
image   | 源用户映像虚拟硬盘
managedDisk | 托管磁盘参数
name    | 磁盘名称
osType  | 磁盘中包含的 OS 类型
vhd     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 writeAccelerator

以下示例演示如何查询 VM 的存储信息。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM 标记

VM 标记包含在实例/计算/标记终结点下的实例 API。
标记可能已应用到 Azure VM 中，以逻辑方式将其归入一个分类。 可使用以下请求检索分配给 VM 的标记。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**响应**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身使用了分号，则此输出可能会出现问题。 如果编写分析程序以编程方式提取标记，则应该依赖于 `tagsList` 字段。 `tagsList` 字段是不带分隔符的 JSON 数组，因此更易于分析。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**响应**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>证明数据

实例元数据服务提供的部分方案是为了保证提供的数据来自 Azure。 我们对此信息的一部分进行签名，以便市场映像可以确保其映像在 Azure 上运行。

### <a name="sample-1-getting-attested-data"></a>示例 1：获取证明数据

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 nonce 值。

Api-version 是必填字段。 有关支持的 API 版本的信息，请参阅[使用部分](#usage)。
Nonce 是一个可选的 10 位字符串。 如果未提供，IMDS 将在其所在位置返回当前 UTC 时间戳。

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

签名 Blob 是 [pkcs7](https://aka.ms/pkcs7) 签名的文档版本。 它包含用于签名的证书以及 VM 详情，如 vmId、sku、nonce、subscriptionId、文档创建和到期的时间戳，以及关于映像的计划信息。 该计划信息只针对 Azure 市场映像进行填充。 证书可从响应中提取，用于验证响应是否有效、是否来自 Azure。
该文档包含以下字段：

数据 | 说明
-----|------------
nonce | 可以随请求提供的可选字符串。 如果未提供 nonce，则会使用当前 UTC 时间戳
plan | [Azure 市场映像计划](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含计划 ID（名称）、产品映像或产品/服务（产品）和发布者 ID（发布者）。
timestamp/createdOn | 创建签名文档时的 UTC 时间戳
timestamp/expiresOn | 签名文档到期时的 UTC 时间戳
vmId |  VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | 虚拟机的 Azure 订阅，引入自 `2019-04-30`
sku | `2019-11-01` 中介绍了 VM 映像的特定 SKU

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>示例 2：验证 VM 是否在 Azure 中运行

市场供应商希望确保其软件仅获许在 Azure 中运行。 如果有人将 VHD 复制到本地，则应当有能力检测到这一情况。 通过调用实例元数据服务，市场供应商可以获得签名数据，以保证响应仅来自 Azure。

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2019-04-30
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

验证签名是否来自 Microsoft Azure 并检查证书链中是否存在错误。

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $conten
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 nonce 值。

如果在初始请求中提供了 nonce 参数，则可以比较签名文档中的 nonce。

> [!NOTE]
> 公有云和每个主权云的证书将有所不同。

云 | 证书
------|------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure 政府](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 用于签名的证书有一个已知问题。 对于公有云，证书可能不完全匹配 `metadata.azure.com`。 因此，证书验证应允许任何 `.metadata.azure.com` 子域中的公用名称。

在验证期间，如果网络限制导致无法下载中间证书，则可以固定中间证书。 不过，Azure 将按照标准 PKI 做法滚动更新证书。 发生滚动更新时，需要更新固定证书。 每当计划进行更改以更新中间证书时，将会更新 Azure 博客并通知 Azure 客户。 [此处](https://www.microsoft.com/pki/mscorp/cps/default.htm)可找到中间证书。 每个区域的中间证书可能不同。

> [!NOTE]
> Azure 中国世纪互联的中间证书将来自 DigiCert 全局根 CA（而不是 Baltimore）。
此外，如果在更改根证书链颁发机构的过程中已固定 Azure 中国的中间证书，则必须更新中间证书。

## <a name="failover-clustering-in-windows-server"></a>Windows Server 中的故障转移群集

在某些情况下，在使用故障转移群集查询实例元数据服务时，必须向路由表添加路由。

1. 使用管理员特权打开命令提示符。

1. 运行以下命令，并记下 IPv4 路由表中网络目标 (`0.0.0.0`) 接口的地址。

```bat
route print
```

> [!NOTE]
> 为简单起见，启用了故障转移群集的 Windows Server VM 的以下示例输出仅包含 IPv4 路由表。

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

运行以下命令并使用网络目标 (`0.0.0.0`) 接口的地址，在此示例中为 `10.0.1.10`。

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity-via-metadata-service"></a>通过元数据服务使用托管标识

可以在 VM 上启用系统分配的托管标识，也可以向 VM 分配一个或多个用户分配的托管标识。
然后，可以从实例元数据服务请求托管标识的令牌。 这些令牌可用于通过其他 Azure 服务（如 Azure Key Vault）进行身份验证。

有关启用此功能的详细步骤，请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events-via-metadata-service"></a>通过元数据服务使用计划事件
可以通过元数据服务获取计划事件的状态，然后用户可以指定要对这些事件执行的一组操作。  有关详细信息，请参阅[计划事件](scheduled-events.md)。 

## <a name="regional-availability"></a>区域可用性

此服务在所有 Azure 云中正式发布。

## <a name="sample-code-in-different-languages"></a>不同语言的示例代码

在 VM 内使用不同语言调用元数据服务的示例：

语言      | 示例
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="error-and-debugging"></a>错误和调试

如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务返回标准 HTTP 错误。 例如：

HTTP 状态代码 | 原因
-----------------|-------
200 正常 |
400 错误的请求 | 查询叶节点时缺少 `Metadata: true` 标头或缺少参数 `format=json`
404 未找到  | 请求的元素不存在
不允许 405 方法 | 仅支持 `GET` 请求
410 不存在 | 在一段时间后重试最长 70 秒
429 请求次数过多 | 该 API 当前支持每秒最多 5 个查询
500 服务错误     | 请稍后重试

### <a name="known-issues-and-faq"></a>已知问题和常见问题解答

1. 我收到错误 `400 Bad Request, Required metadata header not specified`。 这是什么意思呢？
   * 实例元数据服务需要在请求中传递标头 `Metadata: true`。 将该标头传入 REST 调用将允许访问实例元数据服务。
1. 为什么我无法获取我的 VM 的计算信息？
   * 当前实例元数据服务仅支持 Azure Resource Manager 创建的实例。 将来可能会添加对云服务 VM 的支持。
1. 我刚才通过 Azure Resource Manager 创建了我的虚拟机。 为什么我无法看到计算元数据信息？
   * 对于 2016 年 9 月之后创建的所有 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 对于早期 VM（在 2016 年 9 月之前创建的 VM），请在 VM 实例中添加/删除扩展或数据磁盘以刷新元数据。
1. 我看不到为新版本填充的任何数据
   * 对于 2016 年 9 月之后创建的所有 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 对于早期 VM（在 2016 年 9 月之前创建的 VM），请在 VM 实例中添加/删除扩展或数据磁盘以刷新元数据。
1. 我为什么会收到错误 `500 Internal Server Error` 或 `410 Resource Gone`？
   * 基于指数回退系统或[暂时性故障处理](/azure/architecture/best-practices/transient-faults)中所述的其他方法重试请求。 如果问题仍然存在，请在 Azure 门户中为 VM 创建支持问题。
1. 这是否适用于虚拟机规模集实例？
   * 是的，元数据服务可用于规模集实例。
1. 我在虚拟机规模集中更新了我的标记，但与单实例 VM 不同，这些标记未出现在实例中，这是怎么回事？
   * 目前，规模集的标记仅在重启、重置映像或更改实例的磁盘时向 VM 显示。
1. 调用服务时请求超时？
   * 必须从分配给 VM 的主要网卡的主 IP 地址进行元数据调用。 此外，如果你更改了路由，则 VM 的本地路由表中必须存在 169.254.169.254/32 地址的路由。
   * <details>
        <summary>验证路由表</summary>

        1. 转储本地路由表并查找 IMDS 条目（例如）：
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. 验证是否存在 `169.254.169.254` 的路由，并记下相应的网络接口（例如 `172.16.69.7`）。
        1. 转储接口配置并查找与路由表中引用的接口相对应的接口，注明 MAC（物理）地址。
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. 确认该接口对应于 VM 的主 NIC 和主 IP。 可以通过在 Azure 门户中查看网络配置，或[通过 Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show) 查找来找到主 NIC/IP。 记下公共和专用 IP（如果使用 cli，还要记下 MAC 地址）。 PowerShell CLI 示例：
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. 如果它们不匹配，请更新路由表，以使主 NIC/IP 成为目标。
    </details>

## <a name="support-and-feedback"></a>支持和反馈

请在 https://feedback.azure.com 中提交反馈和评论。

若要获取该服务的支持，请针对长时间重试后仍无法获取元数据响应的 VM，在 Azure 门户中创建相关支持问题。
使用“`Management`”作为“问题类型”，然后选择“`Instance Metadata Service`”作为“类别”。

![实例元数据支持](./media/instance-metadata-service/InstanceMetadata-support.png "屏幕截图：在实例元数据服务出现问题时建立支持案例")

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
1.  [获取 VM 的访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。
2.  [计划事件](scheduled-events.md)