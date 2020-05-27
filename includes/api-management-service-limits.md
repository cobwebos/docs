---
title: include 文件
description: include 文件
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204541"
---
| 资源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 最大缩放单位数 | 每个区域 10 个<sup>1</sup> |
| 缓存大小 | 每个单位 5 GiB<sup>2</sup> |
| 每个 HTTP 颁发机构的并行后端连接<sup>3</sup> | 每个单位 2,048 个<sup>4</sup> |
| 最大缓存响应大小 | 2 MiB |
| 最大策略文档大小 | 256 KiB<sup>5</sup> |
| 每个服务实例的最大自定义网关域<sup>6</sup> | 20 个 |
| 每个服务实例的最大 CA 证书数<sup>7</sup> | 10 个 |
| 每个订阅的最大服务实例数<sup>8</sup> | 20 个 |
| 每个服务实例的最大订阅数<sup>8</sup> | 500 |
| 每个服务实例<sup>8</sup>的最大客户端证书数 | 50 |
| 每个服务实例<sup>8</sup>的 api 的最大数目 | 50 |
| 每个服务实例<sup>8</sup>的 API 操作的最大数量 | 1,000 |
| 最大总请求持续时间<sup>8</sup> | 30 秒 |
| 最大缓冲负载大小<sup>8</sup> | 2 MiB |
| 最大请求 URL 大小<sup>9</sup> | 4096 字节 |
| 自承载网关的最大数目为<sup>10</sup> | 25 |

<sup>1</sup>缩放限制取决于定价层。 有关定价层及其缩放限制的详细信息，请参阅[API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每单位缓存大小取决于定价层。 若要查看定价层及其缩放限制，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非被后端明确关闭，否则将共用并重新使用连接。<br/>
<sup>4</sup>此限制按“基本”、“标准”和“高级”层级的单位计算。 开发人员层限制为 1,024。 此限制不适用于消耗层。<br/>
<sup>5</sup>此限制适用于“基本”、“标准”和“高级”层级。 在消耗层中，策略文档大小限制为 4 KiB。<br/>
<sup>6</sup>只有开发人员和高级层支持多个自定义域。<br/>
<sup>7</sup>使用情况层不支持 CA 证书。<br/>
<sup>8</sup>此资源仅适用于消耗层。<br/>
<sup>9</sup>仅适用于消耗层。 包含最长 2048 字节的查询字符串。<br/>
<sup>10</sup>仅开发人员和高级层支持自承载网关。 此限制适用于[自承载的网关资源](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway)数。 若要提高此限制，请联系[支持人员](https://azure.microsoft.com/support/options/)。 请注意，与自承载的网关资源关联的节点数（或副本）在高级层中不受限制，并且在开发人员层的单个节点上。
