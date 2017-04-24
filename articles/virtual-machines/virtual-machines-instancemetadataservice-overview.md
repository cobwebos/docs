---
title: "Azure 实例元数据服务概述 | Microsoft Docs"
description: "一个 RESTful 接口，用于获取有关 VM 计算、网络和即将发生的维护事件的信息。"
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Azure 的实例元数据服务 -- 预览版

> [!NOTE] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [针对 Microsoft Azure 预览版的 Microsoft Azure 补充使用条款。] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

实例元数据服务提供有关正在运行的虚拟机实例的信息。 此信息可用于在 Azure 上管理和配置实例。 Azure 的实例元数据服务是一个 RESTful 终结点，可供通过全新 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 创建的所有 IaaS VM 使用。 该终结点位于已知不可路由的 IP 地址 (*169.254.169.254*)，该地址只能从 VM 中访问。 此服务将提供有关虚拟机实例、其网络配置以及即将发生的维护事件的重要数据。 有关更多信息，请参阅[元数据类别](#instance-metadata-data-categories)。

### <a name="important-information"></a>重要信息
此服务当前处于**预览**状态，并且承载有关虚拟机实例和即将发生的维护事件的信息。 服务将继续接收更新，并且此页将反映可用的特定[数据类别](#instance-metadata-data-categories)。


## <a name="service-availability"></a>服务可用性
当前的预览版在 Azure 的**美国中西部**区域可用。 下面的这一表格将对该服务预览版变得可用的区域进行更新。
若要试用实例元数据服务，请从 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 或 [Azure 门户](http://portal.azure.com)创建 VM，并按照示例部分中的示例访问元数据服务。 

可以使用实例元数据服务预览版的区域|
------------------------------------------------------------|
美国中西部 |



## <a name="retrieving-instance-metadata"></a>检索实例元数据 

实例元数据可用于运行使用 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 创建/管理的 VM。 访问虚拟机实例的所有数据类别时可使用以下 URI

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

所有实例元数据的默认输出均采用 json 格式（内容类型为 Application/json）

## <a name="usage-examples"></a>用法示例
以下是实例元数据服务的一组示例和用法语义

### <a name="versioning"></a>版本控制 
实例元数据服务已进行了版本控制。 版本是必需的，当前的预览版本为 2017-03-01。


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

我们添加更新的版本时，早期版本仍可供访问以保持兼容性（如果你的脚本依赖于特定的数据格式）。 请注意，当前的预览版本在服务正式发布之后可能会不可用。


### <a name="data-output"></a>数据输出
默认情况下实例元数据以 json 格式（内容类型 = application/json）返回数据。不同节点元素可以采用不同的默认格式（如果适用）返回数据，下表是数据格式的快速参考 

元素 | 默认数据格式 | 其他格式
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | 无

对于 text 格式，请在请求 URL 中使用 format=text，例如 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>安全
实例元数据终结点只能从不可路由的 169.254.169.254 IP 地址上正在运行的虚拟机实例中访问。 此外，元数据服务将拒绝任何带有 **X-Forwarded-For 标头**的请求。 我们还要求请求包含 **Metadata:true** 标头，发送该标头是为了确保实际请求是直接计划好的，而不是无意重定向的一部分。 
### <a name="error"></a>错误
如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务将返回标准 HTTP 错误，下面是一些返回代码示例 

HTTP 返回代码 | 原因
----------------|-------
200 | 正常
400 | 错误请求，缺少标头，请传递 -H Metadata:true
404 | 找不到，请求的元素不存在 
405 | 方法不受支持 
429 | 请求过多，目前我们仅支持每秒最多 5 个查询

### <a name="examples"></a>示例
#### <a name="retrieving-the-network-information"></a>检索网络信息 

**请求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**响应**
> [!NOTE] 
> 响应是 json 字符串。 以下输出是使用诸如 [jq](https://stedolan.github.io/jq/) 之类的实用工具设置了格式的 json。
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>检索公共 IP 地址

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>检索实例的所有元数据

**请求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**响应**
> [!NOTE]
> 响应是 json 字符串。 以下输出是使用诸如 [jq](https://stedolan.github.io/jq/) 之类的实用工具设置了格式的 json。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>在 Windows 虚拟机中检索元数据

可以通过 Powershell 实用工具 curl 在 Windows 中检索实例元数据。 在 Powershell 提示符下，运行以下命令： 

**请求**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**响应**
>[!NOTE]
> 响应是 json 字符串。 以下输出是使用 ConvertTo-Json Powershell 实用工具设置了格式的 json。
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>实例元数据数据类别
下表包含一个可通过实例元数据获取的所有数据类别的列表

数据 | 说明
-----|------------
location | 正在运行 VM 的 Azure 区域 
名称 | VM 的名称 
产品/服务 | VM 映像的产品/服务信息，只有从 Azure 映像库部署的映像才存在这些值 
发布者 | VM 映像的发布者
sku | VM 映像的特定 SKU  
版本 | VM 映像的版本 
osType | Linux 或 Windows 
platformUpdateDomain |  正在运行 VM 的[更新域](virtual-machines-windows-manage-availability.md)。 
platformFaultDomain | 正在运行 VM 的[容错域](virtual-machines-windows-manage-availability.md)。
vmId | VM 的唯一标识符，详细信息位于[此处](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [VM 大小](virtual-machines-windows-sizes.md)
ipv4/ipaddress | VM 的本地 IP 地址 
ipv4/publicip | 实例的公共 IP 地址 
subnet/address | 子网地址 
subnet/dnsservers/ipaddress1 | 主 DNS 服务器
subnet/dnsservers/ipaddress2 | 备用 DNS 服务器
subnet/prefix | 子网前缀，例如 24
ipv6/ipaddress | VM 的 IPv6 地址
mac | VM mac 地址 
scheduledevents | 请参阅 [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>用法的示例方案  

### <a name="tracking-vm-running-on-azure"></a>跟踪 Azure 上正在运行的 VM 

作为服务提供商，你可能需要跟踪运行你的软件的 VM 数目，或者你的代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用来自实例元数据服务的 vmId 字段 

**请求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**响应**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>基于容错/更新域放置容器、数据分区 

对于某些方案，在哪里放置不同的副本至关重要。 例如对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)，或者对于通过 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 放置容器，需要知道正在运行 VM 的 platformFaultDomain 和 platformUpdateDomain。 可以直接通过实例元数据查询此数据点

**请求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**响应**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>在支持案例期间获取有关 VM 的详细信息 

作为服务提供商，你可能会接到支持电话，其中你想要了解有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以便支持专业人员了解有关 Azure 上的 VM 类型。 

**请求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**响应**
> [!NOTE] 
> 响应是 json 字符串。 以下输出是使用诸如 [jq](https://stedolan.github.io/jq/) 之类的实用工具设置了格式的 json。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>常见问题
1. 我收到错误“400 错误请求”，“未指定必需的元数据标头”。 这是什么意思呢？
   * 实例元数据服务需要标头 Metadata:true 传入请求中。 将标头传入 REST 调用会允许对实例元数据服务的访问。 
2. 为什么我无法获取我的 VM 的计算信息？
   * 当前实例元数据服务仅支持 Azure Resource Manager 创建的实例，我们可能在将来添加对云服务 VM 的支持。 
3. 我刚才通过 Azure Resource Manager 创建了虚拟机。 为什么我无法看到计算元数据信息？
   * 对于 2016 年 9 月之后创建的任何 VM，请添加[标记](../azure-resource-manager/resource-group-using-tags.md)以开始查看计算元数据。 对于早期 VM（在 2016 年 9 月之前创建），请在 VM 中添加/删除扩展或数据磁盘以刷新元数据。
4. 为什么我收到错误“500 - 内部服务器错误”？
   * 实例元数据预览版当前仅在美国中西部区域可用。 请在支持的区域中部署 VM。  
4. 在哪里共享其他问题/评论？
   * 请在 feedback.azure.com 上发送评论。
    
## <a name="next-steps"></a>后续步骤

了解有关 [scheduledevents] 的详细信息 (virtual-machines-scheduled-events.md)

