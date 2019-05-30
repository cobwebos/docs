---
title: include 文件
description: include 文件
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238821"
---
| 资源 | 限制 |
| --- | --- |
| 最大缩放单位数 | 每个区域 10 个<sup>1</sup> |
| 缓存大小 | 每个单位 5 GB<sup>2</sup> |
| 后端的并发连接<sup>3</sup>每个 HTTP 颁发机构 | 每个单位 2048<sup>4</sup> |
| 最大缓存响应大小 | 2 MB |
| 最大策略文档大小 | 256 KB<sup>5</sup> | 
| 每个服务实例的最大自定义网关域<sup>6</sup> | 20 |
| 最大数量的每个服务实例的 CA 证书 | 10 | 
| 每个订阅的最大服务实例数<sup>7</sup> | 20 | 
| 每个服务实例的最大订阅数<sup>7</sup> | 500 |
| 每个服务实例的最大客户端证书数<sup>7</sup> | 50 | 
| 每个服务实例的最大 API 数<sup>7</sup> | 50 | 
| 每个服务实例的最大 API 操作数<sup>7</sup> | 1,000 | 
| 最大总请求持续时间<sup>7</sup> | 30 秒 | 
| 最大缓冲有效负载大小<sup>7</sup> | 2 MB | 


<sup>1</sup>缩放限制取决于定价层。 若要查看定价层及其缩放限制，请参阅[API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每单位缓存大小取决于定价层。 若要查看定价层及其缩放限制，请参阅[API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>连接建立池连接，除非显式关闭由后端中重复使用。<br/>
<sup>4</sup>此限制是每个单位的基本、 标准和高级层。 开发人员层被限制为 1,024。 此限制不适用于消耗层。<br/> 
<sup>5</sup>此限制适用于基本、 标准和高级层。 在消耗层中，策略文档大小被限制为 4 KB。<br/>
<sup>6</sup>此资源是仅高级层中可用。<br/>
<sup>7</sup>此资源适用于仅消耗级别。<br/>



