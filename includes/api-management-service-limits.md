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
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755531"
---
| 资源 | 限制 |
| --- | --- |
| 缩放单位 | 每个区域 10 个<sup>1</sup> |
| 缓存 | 每个单位 5 GB<sup>1</sup> |
| 每个 HTTP 颁发机构的并行后端连接<sup>2</sup> | 每个单位 2048 个<sup>3</sup> |
| 最大缓存响应大小 | 10MB |
| 最大策略文档大小 | 256KB |
| 最大自定义网关域 | 每个服务实例 20 个<sup>4</sup> |


<sup>1</sup>每个定价层的 API 管理限制不同。 若要查看定价层及其缩放限制，请转到 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。
<sup>2</sup> 除非后端明确关闭，否则将合并并重新使用连接。
<sup>3</sup> 每个单位的基本、标准和高级级别。 开发人员层限制为 1024。
<sup>4</sup> 仅高级层中可用。


