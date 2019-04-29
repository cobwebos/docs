---
title: 使用 REST API 创建 Azure 负载均衡器
titlesuffix: Azure Load Balancer
description: 了解如何使用 REST API 创建 Azure 负载均衡器。
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: 159fe9d6a891858d8d2cc2315e9544b79eb44cff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884973"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>使用 REST API 创建 Azure 基本负载均衡器

Azure 负载均衡器根据规则和运行状况探测，将抵达负载均衡器前端的新入站流量分配到后端池实例。 负载均衡器以两种 SKU 提供：“基本”和“标准”。 若要了解两种 SKU 版本的差异，请参阅[负载均衡器 SKU 比较](load-balancer-overview.md#skus)。
 
本操作方法指南介绍如何使用 [Azure REST API](/rest/api/azure/) 创建 Azure 基本负载均衡器，以便对 Azure 虚拟网络中跨多个 VM 的传入请求进行负载均衡。 [Azure 负载均衡器 REST 参考](/rest/api/load-balancer/)中提供了完整的参考文档和其他示例。
 
## <a name="build-the-request"></a>生成请求
使用以下 HTTP PUT 请求创建新的 Azure 基本负载均衡器。
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI 参数

|名称  |In  |需要 |Type |描述 |
|---------|---------|---------|---------|--------|
|subscriptionId   |  路径       |  True       |   string      |  可以唯一标识 Microsoft Azure 订阅的订阅凭据。 此订阅 ID 是每个服务调用的 URI 的一部分。      |
|resourceGroupName     |     路径    | True        |  string       |   资源组的名称。     |
|loadBalancerName     |  路径       |      True   |    string     |    负载均衡器的名称。    |
|api-version    |   query     |  True       |     string    |  客户端 API 版本。      |



### <a name="request-body"></a>请求正文

唯一必需的参数为 `location`。 如果不定义 *SKU* 版本，则会默认创建基本负载均衡器。  请使用[可选参数](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body)来自定义负载均衡器。

| 名称 | 类型 | 描述 |
| :--- | :--- | :---------- |
| 位置 | string | 资源位置。 使用[列出位置](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations)操作获取位置的当前列表。 |


## <a name="example-create-and-update-a-basic-load-balancer"></a>示例：创建和更新基本负载均衡器

在此示例中，请首先创建基本负载均衡器及其资源。 接下来，请配置负载均衡器资源，其中包括前端 IP 配置、后端地址池、负载均衡规则、运行状况探测，以及入站 NAT 规则。

在使用下面的示例创建负载均衡器之前，请在“美国东部”区域名为 *rg1* 的资源组中使用名为 *subnetlb* 的子网创建名为 *vnetlb* 的虚拟网络。

### <a name="step-1-create-a-basic-load-balancer"></a>步骤 1. 创建基本负载均衡器
在此步骤中，请在“美国东部”位置的 *rg1* 资源组中创建名为 *lb* 的基本负载均衡器。
#### <a name="sample-request"></a>示例请求

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>请求正文

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>步骤 2. 配置负载均衡器资源
在此步骤中，请配置负载均衡器 *lb* 资源，其中包括前端 IP 配置 (*fe-lb*)、后端地址池 (*be-lb*)、负载均衡规则 (*rulelb*)、运行状况探测 (*probe-lb*)，以及入站 NAT 规则 (*in-nat-rule*)。
#### <a name="sample-request"></a>示例请求

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>请求正文

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
