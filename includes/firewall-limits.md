---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52439984"
---
| 资源 | 默认限制 |
| --- | --- |
| 已处理的数据 |1000 TB/防火墙/月 <sup>1</sup> |
|规则|10k - 已组合所有规则类型|
|全球对等互连|不支持。 每个区域应当至少有一个防火墙部署。|
|单个网络规则中的最大端口数|15<br>端口范围（例如：2 - 10）计为两个。
|最小 AzureFirewallSubnet 大小 |/26


<sup>1</sup> 如果需要增加这些限制，请与 Azure 支持部门联系。
