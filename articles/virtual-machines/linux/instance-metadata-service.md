---
title: Azure 实例元数据服务
description: RESTful 接口，用于获取有关 Linux Vm 计算、网络和即将发生的维护事件的信息。
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 3281b4dafa5436c9df760ac8aa3fc82f535b4286
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944863"
---
# <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

Azure 实例元数据服务（IMDS）提供有关当前正在运行的虚拟机实例的信息，可用于管理和配置虚拟机。
提供的信息包括 SKU、网络配置和即将发生的维护事件。 有关可用数据的完整列表，请参阅[元数据 api](#metadata-apis)。

Azure 的实例元数据服务是一个 REST 终结点，可供通过 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)创建的所有 IaaS VM 使用。
该终结点位于已知不可路由的 IP 地址 (`169.254.169.254`)，该地址只能从 VM 中访问。

> [!IMPORTANT]
> 此服务在所有 Azure 区域中提供有正式版。  它会定期更新，发布有关虚拟机实例的新信息。 此页反映了最新可用的[元数据 api](#metadata-apis) 。

## <a name="service-availability"></a>服务可用性

此服务在所有 Azure 区域中提供有可用的正式版。 并非所有 API 版本在所有 Azure 区域中可用。

区域                                        | 可用性？                                 | 支持的版本
-----------------------------------------------|-----------------------------------------------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | 正式版 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 正式版 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure 中国世纪互联](https://www.azure.cn/)                                            | 正式版 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | 正式版 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15

版本2019-11-01 当前已部署，并且在所有区域中均不可用。

当存在服务更新并且/或新支持的版本可用时，此表将会更新。

若要试用实例元数据服务，请在上述区域中从 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)或 [Azure 门户](https://portal.azure.com)创建一个 VM，并按照以下示例操作。
有关如何查询 IMDS 的更多示例，请参阅[Azure 实例元数据示例](https://github.com/microsoft/azureimds)

## <a name="usage"></a>使用情况

### <a name="versioning"></a>版本控制

实例元数据服务是版本控制的，在 HTTP 请求中指定 API 版本是必需的。

你可以看到此[可用性表](#service-availability)中列出的最新版本。

在添加更新的版本时，早期版本仍可供访问以保持兼容性（如果脚本依赖于特定的数据格式）。

当未指定版本时，将返回一个错误，其中包含最新的受支持版本的列表。

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
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

### <a name="using-headers"></a>使用标头

查询实例元数据服务时，必须提供标头 `Metadata: true`，以确保不会意外将请求重定向。

### <a name="retrieving-metadata"></a>检索元数据

实例元数据可用于运行使用 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)创建/管理的 VM。 使用以下请求访问虚拟机实例的所有数据类别：

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> 所有实例元数据查询都要区分大小写。

### <a name="data-output"></a>数据输出

默认情况下，实例元数据服务以 JSON 格式返回数据 (`Content-Type: application/json`)。 但是，如果提出请求，不同 API 可以其他格式返回数据。
下表为 API 可能支持的其他数据格式的参考。

API | 默认数据格式 | 其他格式
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | none
/attested | json | none

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 对于叶节点，`format=json` 不起作用。 对于这些查询，如果默认格式为 json，则需要显式指定 `format=text`。

### <a name="security"></a>安全性

只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问实例元数据服务终结点。 此外，服务会拒绝任何带有 `X-Forwarded-For` 标头的请求。
请求必须包含 `Metadata: true` 标头，以确保实际请求是直接计划好的，而不是无意重定向的一部分。

### <a name="error"></a>错误

如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务将返回标准 HTTP 错误。 例如：

HTTP 状态代码 | 原因
----------------|-------
200 正常 |
400 错误请求 | 查询叶节点时缺少 `Metadata: true` 标头或缺少格式
404 未找到 | 请求的元素不存在
不允许使用 405 方法 | 仅支持 `GET` 请求
410 不存在 | 在一段时间后重试，最大为70秒
429 请求过多 | 目前该 API 每秒最多支持 5 个查询
500 服务错误     | 请稍后重试

### <a name="examples"></a>示例

> [!NOTE]
> 所有 API 响应均为 JSON 字符串。 为了便于阅读，所有以下示例响应都具有良好的打印效果。

#### <a name="retrieving-network-information"></a>检索网络信息

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**响应**

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

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

#### <a name="retrieving-public-ip-address"></a>检索公共 IP 地址

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>检索实例的所有元数据

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**响应**

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
  "compute": {
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
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>元数据 Api

以下 Api 可通过元数据终结点获得：

data | 说明 | 引入的版本
-----|-------------|-----------------------
attested | 请参阅[证明数据](#attested-data) | 2018-10-01
identity | Azure 资源的托管标识。 请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | 请参阅[实例 API](#instance-api) | 2017-04-02
scheduledevents | 请参阅[计划事件](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>实例 API

可以通过实例 API 使用以下计算类别：

> [!NOTE]
> 通过元数据终结点，可通过实例/计算访问以下类别

data | 说明 | 引入的版本
-----|-------------|-----------------------
azEnvironment | VM 在其中运行的 Azure 环境 | 2018-10-01
customData | 此功能当前处于禁用状态，我们将在此文档可用时更新它 | 2019-02-01
location | 正在运行 VM 的 Azure 区域 | 2017-04-02
name | VM 的名称 | 2017-04-02
offer | 为 VM 映像提供信息，并且仅适用于从 Azure 映像库部署的映像 | 2017-04-02
osType | Linux 或 Windows | 2017-04-02
placementGroupId | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
计划 | 包含 VM 的名称、产品和发布者的[计划](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)（如果它是 Azure Marketplace 映像） | 2018-04-02
platformUpdateDomain |  正在运行 VM 的[更新域](manage-availability.md) | 2017-04-02
platformFaultDomain | 正在运行 VM 的[容错域](manage-availability.md) | 2017-04-02
provider | VM 的提供程序 | 2018-10-01
publicKeys | 分配给 VM 和路径[的公钥的集合](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
发布者 | VM 映像的发布者 | 2017-04-02
resourceGroupName | 虚拟机的[资源组](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | 资源的[完全限定](https://docs.microsoft.com/rest/api/resources/resources/getbyid)ID | 2019-03-11
sku | VM 映像的特定 SKU | 2017-04-02
storageProfile | 请参阅[存储配置文件](#storage-profile) | 2019-06-01
subscriptionId | 虚拟机的 Azure 订阅 | 2017-08-01
标记 | 虚拟机的[标记](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | 格式化为 JSON 数组以方便编程分析的标记  | 2019-06-04
版本 | VM 映像的版本 | 2017-04-02
vmId | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | 虚拟机规模集的虚拟机规模集[名称](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [VM 大小](sizes.md) | 2017-04-02
区域 | 虚拟机的[可用性区域](../../availability-zones/az-overview.md) | 2017-12-01

以下网络类别可通过实例 API 使用：

> [!NOTE]
> 通过元数据终结点，可通过实例/网络/接口访问以下类别

data | 说明 | 引入的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本地 IPv4 地址 | 2017-04-02
ipv4/publicIpAddress | VM 的公共 IPv4 地址 | 2017-04-02
subnet/address | VM 的子网地址 | 2017-04-02
subnet/prefix | 子网前缀，例如 24 | 2017-04-02
ipv6/ipaddress | VM 的本地 IPv6 地址 | 2017-04-02
macAddress | VM mac 地址 | 2017-04-02

## <a name="attested-data"></a>证明数据

实例元数据服务提供的方案的一部分是保证提供的数据来自 Azure。 我们对此信息的一部分进行签名，以便市场映像可以确保其映像在 Azure 上运行。

### <a name="example-attested-data"></a>示例证明数据

> [!NOTE]
> 所有 API 响应均为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

 **请求**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-版本为必填字段。 有关支持的 API 版本，请参阅[服务可用性部分](#service-availability)。
Nonce 是一个可选的10位字符串。 如果未提供，IMDS 将在其位置返回当前 UTC 时间戳。 由于 IMDS 的缓存机制，可能会返回以前缓存的 nonce 值。

 **响应**

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

签名 Blob 是 [pkcs7](https://aka.ms/pkcs7) 签名的文档版本。 它包含用于签名的证书和 VM 详细信息，如 vmId、sku、nonce、subscriptionId、用于创建和过期文档的时间戳以及有关映像的计划信息。 计划信息只针对 Azure 市场映像填充。 证书可从响应中提取，用于验证响应是否有效、是否来自 Azure。

## <a name="example-scenarios-for-usage"></a>用法的示例方案  

### <a name="tracking-vm-running-on-azure"></a>跟踪 Azure 上正在运行的 VM

作为服务提供商，可能需要跟踪运行软件的 VM 数目，或者代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用实例元数据服务的 `vmId` 字段。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>基于容错/更新域放置容器、数据分区

对于某些方案，不同的数据副本的位置至关重要。 例如，对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或通过 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 实现容器放置时，需要知道正在运行 VM 的 `platformFaultDomain` 和 `platformUpdateDomain`。
你还可以使用实例的[可用性区域](../../availability-zones/az-overview.md)来做出这些决策。
可直接通过实例元数据服务查询此数据。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**响应**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会接到支持电话，会告知想要了解的有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以便支持专业人员了解有关 Azure 上的 VM 类型。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**响应**

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>获取 VM 所在的 Azure 环境

Azure 具有各种主权云，如 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)。 有时你需要使用 Azure 环境来做出一些运行时决策。 以下示例显示了如何实现此行为。

**请求**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**响应**

```bash
AzurePublicCloud
```

下面列出了 Azure 环境和 Azure 环境的值。

 云   | Azure 环境
---------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>正在获取 VM 的标记

标记可能已应用到 Azure VM 以逻辑方式将它们组织到分类。 可以使用以下请求检索分配给 VM 的标记。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**响应**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身中使用了分号，这可能是一个问题。 如果编写分析器以编程方式提取标记，则应该依赖于不带分隔符的 JSON 数组的 `tagsList` 字段，因此更易于分析。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
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

### <a name="validating-that-the-vm-is-running-in-azure"></a>验证 VM 是否在 Azure 中运行

市场供应商希望确保其软件仅获许在 Azure 中运行。 如果有人将 VHD 复制到本地，则他们应该能够检测到这一点。 通过调用实例元数据服务，市场供应商可以获得签名数据，以保证响应仅来自 Azure。

> [!NOTE]
> 需要安装 jq。

**请求**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **响应**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

data | 说明
-----|------------
nonce | 用户提供了带有请求的可选字符串。 如果请求中未提供 nonce，则返回当前 UTC 时间戳
计划 | 在 Azure 市场映像中 VM 的[计划](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)，包含名称、产品和发布者
timestamp/createdOn | 创建第一个签名文档时的 UTC 时间戳
timestamp/expiresOn | 签名文档过期时的 UTC 时间戳
vmId |  VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | 虚拟机的 Azure 订阅，在 `2019-04-30` 中引入
sku | `2019-11-01` 中引入的 VM 映像的特定 SKU

#### <a name="verifying-the-signature"></a>验证签名

获得上面的签名后，可以验证签名是否来自 Microsoft。 还可以验证中间证书和证书链。 最后，可以验证订阅 ID 是否正确。

> [!NOTE]
> 公有云和主权云的证书将有所不同。

 云 | 证书
---------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *. metadata.azure.us
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)         | *. metadata.azure.cn
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | *. metadata.microsoftazure.de

用于签名的证书有一个已知问题。 对于公有云，证书可能不完全匹配 `metadata.azure.com`。 因此，证书验证应允许任何 `.metadata.azure.com` 子域中的公用名称。

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

如果在验证期间由于网络限制而无法下载中间证书，则可以固定中间证书。 但是，Azure 将根据标准 PKI 实践滚动更新证书。 发生滚动更新时，需要更新固定的证书。 每当计划更新中间证书的更改时，将更新 Azure 博客并通知 Azure 客户。 可在[此处](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到中间证书。 每个区域的中间证书可能不同。

### <a name="storage-profile"></a>存储配置文件

实例元数据服务可以提供有关与 VM 关联的存储磁盘的详细信息。 此数据可在实例/计算/storageProfile 终结点上找到。

VM 的存储配置文件分为三个类别：映像引用、操作系统磁盘和数据磁盘。

Image reference 对象包含有关 OS 映像的以下信息：

data    | 说明
--------|-----------------
id      | 资源 ID
offer   | 平台或 marketplace 映像的产品/服务
发布者 | 映像发布者
sku     | 图像 sku
版本 | 平台或 marketplace 映像的版本

OS 磁盘对象包含有关 VM 使用的 OS 磁盘的以下信息：

data    | 说明
--------|-----------------
缓存 | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小（GB）
image   | 源用户映像虚拟硬盘
lun     | 磁盘的逻辑单元号
managedDisk | 托管磁盘参数
name    | 磁盘名称
硬盘     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 writeAccelerator

数据磁盘数组包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象都包含以下信息：

data    | 说明
--------|-----------------
缓存 | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小（GB）
encryptionSettings | 磁盘的加密设置
image   | 源用户映像虚拟硬盘
managedDisk | 托管磁盘参数
name    | 磁盘名称
osType  | 磁盘中包含的操作系统类型
硬盘     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 writeAccelerator

下面的示例演示如何查询 VM 的存储信息。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**响应**

> [!NOTE]
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>在 VM 内使用不同语言调用元数据服务的示例

语言 | 示例
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>常见问题解答

1. 出现 `400 Bad Request, Required metadata header not specified` 错误。 这是什么意思呢？
   * 实例元数据服务要求将标头 `Metadata: true` 传入请求。 将此标头传入 REST 调用即可访问实例元数据服务。
2. 为什么我无法获取我的 VM 的计算信息？
   * 目前，实例元数据服务仅支持使用 Azure 资源管理器创建的实例。 将来可能会添加对云服务 VM 的支持。
3. 我刚才通过 Azure 资源管理器创建了虚拟机。 为什么我无法看到计算元数据信息？
   * 对于 2016 年 9 月之后创建的任何 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 对于早期 VM（创建于 2016 年 9 月前），请在 VM 中添加/删除扩展或数据磁盘，刷新元数据。
4. 我看不到为新版本填充的任何数据
   * 对于 2016 年 9 月之后创建的任何 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 对于早期 VM（创建于 2016 年 9 月前），请在 VM 中添加/删除扩展或数据磁盘，刷新元数据。
5. 为什么会出现 `500 Internal Server Error` 错误？
   * 请根据指数后退系统重试请求。 如果仍存在问题，请联系 Azure 支持部门。
6. 在哪里共享其他问题/评论？
   * 在 https://feedback.azure.com 上发送评论。
7. 这是否适用于虚拟机规模集实例？
   * 适用，元数据服务可用于规模集实例。
8. 如何获取服务支持？
   * 若要获取该服务的支持，请针对长时间重试后仍无法获取元数据响应的 VM，在 Azure 门户中创建相关支持问题。
9. 调用服务时请求超时？
   * 必须从分配给 VM 主要网卡的主 IP 地址进行元数据调用，另外，如果更改了路由，则必须为网络卡的 "169.254.0.0/16" 地址提供路由。
10. 我更新了虚拟机规模集中的标记，但与 VM 不同，它们未显示在实例中，这是怎么回事？
    * 目前，对于规模集，仅在重启/重置映像/或对实例的磁盘更改时，向 VM 显示标记。

    ![实例元数据支持](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>后续步骤

- 详细了解[计划事件](scheduled-events.md)
