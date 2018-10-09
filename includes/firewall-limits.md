---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47186173"
---
| 资源 | 默认限制 |
| --- | --- |
| 已处理的数据 |1000 TB/防火墙/月 <sup>1</sup> |
|规则|10000 个应用程序规则，10000 个网络规则|
|VNet 对等互连|对于中心和辐射实现，最多有 50 个辐射 VNET。|
|全球对等互连|不支持。 每个区域应当至少有一个防火墙部署。|
|单个网络规则中的最大端口数|15<br>端口范围（例如：2 - 10）计为两个。


<sup>1</sup> 如果需要增加这些限制，请与 Azure 支持部门联系。
