---
title: include 文件
description: include 文件
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 03/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 99766ca4cc9d77927030f81cff6bb9c009874f89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 <sup>1</sup> |20 |10,000 |
| 每个订阅的[共同管理员数](../articles/billing-add-change-azure-subscription-administrator.md) |200 |200 |
| 每个订阅在每个区域中的[存储帐户数](../articles/storage/common/storage-create-storage-account.md)<sup>2</sup> |200 |250 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |20 |200 |
| 每个订阅的[本地网络数](http://msdn.microsoft.com/library/jj157100.aspx) |10 |500 |
| 每个订阅的 SQL 数据库服务器数 |6 |150 |
| 每个订阅的 DNS 服务器 |9 |100 |
| 每个订阅的保留的 IP |20 |100 |
| 每个订阅的托管服务证书数 |199 |199 |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |256 |256 |


<sup>1</sup>特小实例作为一项核心至核心限制计数，即使使用了部分核心。

<sup>2</sup>此存储帐户限制包括标准和高级存储帐户。 如果在单个区域中需要的存储帐户多于 200 个，请通过 [Azure 支持](https://azure.microsoft.com/support/faq/)提出请求。 Azure 存储团队将评审业务案例，最多可以批准 250 个存储帐户。 

