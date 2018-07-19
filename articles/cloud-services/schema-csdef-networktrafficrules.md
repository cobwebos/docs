---
title: Azure 云服务定义NetworkTrafficRules 架构 | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 71c791c9ac6f679f0f67b014c8fb5dd915d1a3e3
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004398"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure 云服务定义 NetworkTrafficRules 架构
`NetworkTrafficRules` 节点是服务定义文件中的一个可选元素，用于指定角色如何彼此进行通信。 它限制哪些角色可以访问特定角色的内部终结点。 `NetworkTrafficRules` 不是一个独立的元素；它在服务定义文件中与两个或更多角色结合使用。

服务定义文件的默认扩展名为 .csdef。

> [!NOTE]
>  仅当使用 Azure SDK 1.3 版或更高版本时，才可以使用 `NetworkTrafficRules` 节点。

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>网络流量规则的基本服务定义架构
包含网络流量定义的服务定义文件的基本格式如下所示。

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>架构元素
服务定义文件的 `NetworkTrafficRules` 节点包含以下元素，本主题的后续部分做了详细说明：

[NetworkTrafficRules 元素](#NetworkTrafficRules)

[OnlyAllowTrafficTo 元素](#OnlyAllowTrafficTo)

[Destinations 元素](#Destinations)

[RoleEndpoint 元素](#RoleEndpoint)

[AllowAllTraffic 元素](#AllowAllTraffic)

[WhenSource 元素](#WhenSource)

[FromRole 元素](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules 元素
`NetworkTrafficRules` 元素指定哪些角色可以与另一角色上的哪个终结点进行通信。 服务可以包含一个 `NetworkTrafficRules` 定义。

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 元素
`OnlyAllowTrafficTo` 元素描述目标终结点的集合以及可以与它们进行通信的角色。 可以指定多个 `OnlyAllowTrafficTo` 节点。

##  <a name="Destinations"></a> Destinations 元素
`Destinations` 元素描述可以与之进行通信的 RoleEndpoint 的集合。

##  <a name="RoleEndpoint"></a> RoleEndpoint 元素
`RoleEndpoint` 元素描述角色上允许与之进行通信的终结点。 如果角色上有多个终结点，可以指定多个 `RoleEndpoint` 元素。

| 属性      | Type     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 必需。 允许将流量发送到的终结点的名称。|
| `roleName`     | `string` | 必需。 允许与之进行通信的 web 角色的名称。|

## <a name="allowalltraffic-element"></a>AllowAllTraffic 元素
`AllowAllTraffic` 元素是一个规则，它允许所有角色与 `Destinations` 节点中定义的终结点进行通信。

##  <a name="WhenSource"></a> WhenSource 元素
`WhenSource` 元素描述可以与 `Destinations` 节点中定义的终结点进行通信的角色的集合。

| 属性 | Type     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | 必需。 指定允许通信时要应用的规则。 目前唯一有效的值是 `AnyRule`。|
  
##  <a name="FromRole"></a> FromRole 元素
`FromRole` 元素指定可以与 `Destinations` 节点中定义的终结点进行通信的角色。 如果有多个可以与终结点进行通信的角色，可以指定多个 `FromRole` 元素。

| 属性  | Type     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 必需。 允许从中进行通信的角色的名称。|

## <a name="see-also"></a>另请参阅
[云服务（经典）定义架构](schema-csdef-file.md)