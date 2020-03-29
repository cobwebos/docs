---
title: include 文件
description: include 文件
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159459"
---
| 资源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 最大缩放单位数 | 每个区域 10 个<sup>1</sup> |
| 缓存大小 | 每单位 5 GiB<sup>2</sup> |
| 每个 HTTP 权限的并发后端连接<sup>3</sup> | 每单位 2，048<sup>4</sup> |
| 最大缓存响应大小 | 2 米B |
| 最大策略文档大小 | 256 KiB<sup>5</sup> |
| 每个服务实例的最大自定义网关域<sup>6</sup> | 20 |
| 每个服务实例的最大 CA 证书数 | 10 |
| 每个订阅的最大服务实例数<sup>7</sup> | 20 |
| 每个服务实例的最大订阅数<sup>7</sup> | 500 |
| 每个服务实例的最大客户端证书数<sup>7</sup> | 50 |
| 每个服务实例的最大 API 数<sup>7</sup> | 50 |
| 每个服务实例的最大 API 操作数<sup>7</sup> | 1,000 |
| 最大总请求持续时间<sup>7</sup> | 30 秒 |
| 最大缓冲有效负载大小<sup>7</sup> | 2 米B |
| 最大请求 URL 大小<sup>8</sup> | 4096 字节 |

<sup>1</sup>缩放限制取决于定价层。 要查看定价层及其缩放限制，请参阅[API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>单位缓存大小取决于定价层。 要查看定价层及其缩放限制，请参阅[API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非后端显式关闭，否则连接将汇集并重用。<br/>
<sup>4</sup>此限制是基本、标准和高级层的单位。 开发人员层限制为 1，024。 此限制不适用于"消耗"层。<br/>
<sup>5</sup>此限制适用于基本、标准和高级层。 在"消费"层中，策略文档大小限制为 4 KiB。<br/>
<sup>6</sup>此资源仅在高级层中可用。<br/>
<sup>7</sup>此资源仅适用于"消耗"层。<br/>
<sup>8</sup>仅适用于"消耗"层。 包括最多 2048 字节长的查询字符串。<br/>
