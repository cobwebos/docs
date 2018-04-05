---
title: Azure 实例元数据服务 | Microsoft 文档
description: 一个 RESTful 接口，用于获取有关 Windows VM 计算、网络和即将发生的维护事件的信息。
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: timlt
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: e2a449442b766722213d2d9f279ff4a628ec3008
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务


Azure 实例元数据服务提供有关运行虚拟机实例的信息，这些实例可用于管理和配置虚拟机。
这些信息中包括 SKU、网络配置和即将发送的维护事件等相关信息。 如要深入了解可用信息类型，请参阅[元数据类别](#instance-metadata-data-categories)。

Azure 的实例元数据服务是一个 REST 终结点，可供通过 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)创建的所有 IaaS VM 使用。 该终结点位于已知不可路由的 IP 地址 (`169.254.169.254`)，该地址只能从 VM 中访问。

> [!IMPORTANT]
> 此服务在所有 Azure 区域中提供有正式版。  它会定期更新，发布有关虚拟机实例的新信息。 此页显示了最新可用的[数据类别](#instance-metadata-data-categories)。

## <a name="service-availability"></a>服务可用性
此服务在所有 Azure 区域中提供有可用的正式版。 并非所有 API 版本在所有 Azure 区域中可用。

区域                                        | 可用性？                                 | 支持的版本
-----------------------------------------------|-----------------------------------------------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | 正式版   | 2017-04-02, 2017-08-01, 2017-12-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 正式版 | 2017-04-02,2017-08-01
[Azure 中国：](https://www.azure.cn/)                                                           | 正式版 | 2017-04-02,2017-08-01
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | 正式版 | 2017-04-02,2017-08-01

当有服务更新和/或有可用的新支持版本时，此表将更新

若要试用实例元数据服务，请在上述区域中从 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)或 [Azure 门户](http://portal.azure.com)创建一个 VM，并按照以下示例操作。

## <a name="usage"></a>使用情况

### <a name="versioning"></a>版本控制
已对实例元数据服务进行了版本控制。 版本是必需的，全局 Azure 上的当前版本为 `2017-12-01`。 当前支持的版本为（2017-04-02、2017-08-01、2017-12-01）

> [!NOTE] 
> 支持的计划事件的早期预览版发布 {最新} 为 api-version。 此格式不再受支持，并且会在未来被弃用。

在添加更新的版本时，早期版本仍可供访问以保持兼容性（如果脚本依赖于特定的数据格式）。 但是，在服务正式发布之后，先前的预览版本（2017-03-01）可能不再可用。

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
/scheduledevents | json | 无

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>“安全”
只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问实例元数据服务终结点。 此外，服务会拒绝任何带有 `X-Forwarded-For` 标头的请求。
请求必须包含 `Metadata: true` 标头，以确保实际请求是直接计划好的，而不是无意重定向的一部分。 

### <a name="error"></a>错误
如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务将返回标准 HTTP 错误。 例如：

HTTP 状态代码 | 原因
----------------|-------
200 正常 |
400 错误请求 | 缺少 `Metadata: true` 标头
404 未找到 | 请求的元素不存在 
不允许使用 405 方法 | 仅支持 `GET` 和 `POST` 请求
429 请求过多 | 目前该 API 每秒最多支持 5 个查询
500 服务错误     | 请稍后重试

### <a name="examples"></a>示例

> [!NOTE] 
> 所有 API 响应均为 JSON 字符串。 以下所有示例响应显示清晰，可供阅读。

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
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**响应**

> [!NOTE] 
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>在 Windows 虚拟机中检索元数据

**请求**

可通过 `curl` 程序在 Windows 中检索实例元数据： 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

还可以通过 `Invoke-RestMethod` PowerShell cmdlet 检索：
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**响应**

> [!NOTE] 
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>实例元数据数据类别
可通过实例元数据服务获取以下数据类别：

数据 | 说明 | 引入的版本 
-----|-------------|-----------------------
location | 正在运行 VM 的 Azure 区域 | 2017-04-02 
名称 | VM 的名称 | 2017-04-02
offer | 为 VM 映像提供信息。 仅为从 Azure 映像库部署的映像显示此值。 | 2017-04-02
发布者 | VM 映像的发布者 | 2017-04-02
sku | VM 映像的特定 SKU | 2017-04-02
版本 | VM 映像的版本 | 2017-04-02
osType | Linux 或 Windows | 2017-04-02
platformUpdateDomain |  正在运行 VM 的[更新域](manage-availability.md) | 2017-04-02
platformFaultDomain | 正在运行 VM 的[容错域](manage-availability.md) | 2017-04-02
vmId | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmSize | [VM 大小](sizes.md) | 2017-04-02
subscriptionId | 虚拟机的 Azure 订阅 | 2017-08-01
标记 | 虚拟机的[标记](../../azure-resource-manager/resource-group-using-tags.md)  | 2017-08-01
resourceGroupName | 虚拟机的[资源组](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
placementGroupId | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
vmScaleSetName | 虚拟机规模集的 [Virtual Machine ScaleSet Name] (../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
区域 | 虚拟机的[可用性区域](../../availability-zones/az-overview.md) | 2017-12-01 
ipv4/privateIpAddress | VM 的本地 IPv4 地址 | 2017-04-02
ipv4/publicIpAddress | VM 的公共 IPv4 地址 | 2017-04-02
subnet/address | VM 的子网地址 | 2017-04-02 
subnet/prefix | 子网前缀，例如 24 | 2017-04-02 
ipv6/ipaddress | VM 的本地 IPv6 地址 | 2017-04-02 
macAddress | VM mac 地址 | 2017-04-02 
scheduledevents | 请参阅[计划事件](scheduled-events.md) | 2017-08-01

## <a name="example-scenarios-for-usage"></a>用法的示例方案  

### <a name="tracking-vm-running-on-azure"></a>跟踪 Azure 上正在运行的 VM

作为服务提供商，可能需要跟踪运行软件的 VM 数目，或者代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用实例元数据服务的 `vmId` 字段。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>基于容错/更新域放置容器、数据分区 

对于某些方案，不同的数据副本的位置至关重要。 例如，对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或通过 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 实现容器放置时，需要知道正在运行 VM 的 `platformFaultDomain` 和 `platformUpdateDomain`。
你还可以利用实例的[可用性区域](../../availability-zones/az-overview.md)来做出这些决策。
可直接通过实例元数据服务查询此数据。

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**响应**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>在支持案例期间获取有关 VM 的详细信息 

作为服务提供商，你可能会接到支持电话，会告知想要了解的有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以便支持专业人员了解有关 Azure 上的 VM 类型。 

**请求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**响应**

> [!NOTE] 
> 响应为 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
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
    

## <a name="faq"></a>常见问题
1. 出现 `400 Bad Request, Required metadata header not specified` 错误。 这是什么意思呢？
   * 实例元数据服务要求将标头 `Metadata: true` 传入请求。 将此标头传入 REST 调用即可访问实例元数据服务。 
2. 为什么我无法获取我的 VM 的计算信息？
   * 目前，实例元数据服务仅支持使用 Azure 资源管理器创建的实例。 将来可能会添加对云服务 VM 的支持。
3. 我刚才通过 Azure 资源管理器创建了虚拟机。 为什么我无法看到计算元数据信息？
   * 对于 2016 年 9 月之后创建的任何 VM，请添加[标记](../../azure-resource-manager/resource-group-using-tags.md)以开始查看计算元数据。 对于早期 VM（创建于 2016 年 9 月前），请在 VM 中添加/删除扩展或数据磁盘，刷新元数据。
4. 我看不到为新版本 2017-08-01 填充的任何数据
   * 对于 2016 年 9 月之后创建的任何 VM，请添加[标记](../../azure-resource-manager/resource-group-using-tags.md)以开始查看计算元数据。 对于早期 VM（创建于 2016 年 9 月前），请在 VM 中添加/删除扩展或数据磁盘，刷新元数据。
5. 为什么会出现 `500 Internal Server Error` 错误？
   * 请根据指数后退系统重试请求。 如果仍存在问题，请联系 Azure 支持部门。
6. 在哪里共享其他问题/评论？
   * 在 http://feedback.azure.com 上发送评论。
7. 这是否适用于虚拟机规模集实例？
   * 适用，元数据服务可用于规模集实例。 
8. 如何获取服务支持？
   * 若要获取该服务的支持，请针对长时间重试后仍无法获取元数据响应的 VM，在 Azure 门户中创建相关支持问题 

   ![实例元数据支持](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>后续步骤

- 详细了解[计划事件](scheduled-events.md)
