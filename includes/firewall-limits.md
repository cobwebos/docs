---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429926"
---
| 资源 | 默认限制 |
| --- | --- |
| 已处理的数据 |1000 TB/防火墙/月 <sup>1</sup> |
|规则|10k - 已组合所有规则类型|
|全球对等互连|不支持。 每个区域应当至少有一个防火墙部署。|
|单个网络规则中的最大端口数|15<br>端口范围（例如：2 - 10）计为两个。
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|0-64,000。 目前正在努力放宽此限制。|
|


<sup>1</sup> 如果需要增加这些限制，请与 Azure 支持部门联系。
