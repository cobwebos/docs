---
title: 配置网关路由请求 | Microsoft Docs
description: 了解如何配置网关，以处理在 Service Fabric 网格上运行的应用程序的传入流量。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 1c75d781c8a83b54ac9474c83388cf02b5d03e3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532076"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>配置网关资源以路由请求

网关资源用于将传入的流量路由到存储应用程序的网络。 配置以指定根据请求结构将请求定向到特定服务或终结点的规则。 有关网格中的网络和网关的详细信息，请参阅 [Service Fabric 网格中的网络简介](service-fabric-mesh-networks-and-gateways.md)。 

网关资源需要声明为部署模板（JSON 或 yaml）的一部分，并且依赖于网络资源。 本文档概述了可以为网关设置的各种属性，并介绍了一个示例网关配置。

## <a name="options-for-configuring-your-gateway-resource"></a>配置网关资源的选项

因为网关资源充当应用程序网络和底层基础结构网络（`open` 网络）之间的桥梁。 应只需配置一个（在网格预览中，存在每个应用程序对应一个网关的限制）。 资源声明由两个主要部分组成：资源元数据和属性。 

### <a name="gateway-resource-metadata"></a>网关资源元数据

网关使用以下元数据声明：
* `apiVersion` - 需设置为“2018-09-01 预览版”（在将来可能为更高版本）
* `name` - 此网关的字符串名称
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - 应设置为你的应用程序/网络位置；通常是对部署中位置参数的引用
* `dependsOn` - 此网关将作为入口点的网络

在 Azure 资源管理器 (JSON) 部署模板中，它是这样的： 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>网关属性

属性部分用于定义网关所在的网络以及传递请求的规则。 

#### <a name="source-and-destination-network"></a>源和目标网络 

每个网关都需要一个 `sourceNetwork` 和 `destinationNetwork`。 源网络定义为应用程序接将从中接收入站请求的网络。 其名称属性应始终设置为“开放”。 目标网络是请求所针对的网络。 此名称值应设置为应用程序本地网络的资源名称（应包括对资源的完整引用）。 下面是在名为“myNetwork”的网络中部署时的配置示例。

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>规则 

网关可以有多个传递规则，指定如何处理传入的通信。 传递规则为给定的应用程序定义监听端口和目标终结点之间的关系。 对于 TCP 传递规则，端口和终结点之间存在 1 对 1 映射。 对于 HTTP 传递规则，可以设置更复杂的传递规则，这些规则检查请求的路径和（可选）标头，以决定如何传递请求。 

针对每个端口来指定传递规则。 每个入口端口在网关配置的属性部分都有自己的规则数组。 

#### <a name="tcp-routing-rules"></a>TCP 传递规则 

TCP 传递规则包括以下属性： 
* `name` - 引用规则，该规则可以是所选的任何字符串 
* `port` - 侦听传入请求的端口 
* `destination` - 终结点规范，其中包括 `applicationName`、`serviceName` 和 `endpointName`，用于请求需要路由到的位置

下面是 TCP 传递规则示例：

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP 传递规则 

HTTP 传递规则包括以下属性： 
* `name` - 引用规则，该规则可以是所选的任何字符串 
* `port` - 侦听传入请求的端口 
* `hosts` - 一组策略，适用于到达上述指定端口上各个“主机”的请求。 主机是一组应用程序和服务，这些应用程序和服务可能在网络中运行，并且可以处理传入请求，即 Web 应用程序。 主机策略按顺序给予解释，因此应按照降序特征级别创建以下内容
    * `name` - 指定以下传递规则的主机的 DNS 名称。 在此使用“*”将为所有主机创建传递规则。
    * `routes` - 针对此特定主机的一组策略
        * `match` - 基于 `path`，用于应用此规则的传入请求结构的规范
            * `path` - 包含 `value`（传入 URI）、`rewrite`（希望如何转发请求）和 `type`（当前只能是“Prefix”）
            * `header` - 是一组可选标头值，如果请求与路径规范（上述）匹配，则在请求标头中匹配。
              * 每个条目均包含 `name`（要匹配的标头的字符串名称）、`value`（请求中标头的字符串值），和 `type`（当前只能是“Exact”）
        * `destination` - 如果请求匹配，它将被路由到此目的地，此目的地是使用 `applicationName`、`serviceName` 和 `endpointName` 指定的

下面是一个 HTTP 传递规则示例，它适用于端口 80 上的传入请求，以及该网络中应用程序提供的所有主机。 如果请求 URL 具有与路径规范匹配的结构，即 `<IPAddress>:80/pickme/<requestContent>`，则它将被定向到 `myListener` 终结点。  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>网关资源配置示例 

完整的网关资源配置如下所示（改编自[网格示例存储库](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)中提供的入口示例）：

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

此网关是为 Linux 应用程序“meshAppLinux”配置的，该应用程序由至少两个服务组成，“helloWorldService”和“counterService”，在端口 80 上侦听。 根据传入请求的 URL 结构，它将请求路由到其中一项服务。 
* “<IPAddress>:80/helloWorld/\<request\>”会导致请求被定向到 helloWorldService 中的“helloWorldListener”。 
* “<IPAddress>:80/counter/\<request\>”会导致请求被定向到 counterService 中的“counterListener”。 

## <a name="next-steps"></a>后续步骤
* 部署[入口示例](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress)以查看操作中的网关
