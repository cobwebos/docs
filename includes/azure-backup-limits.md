---
title: include 文件
description: include 文件
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300616"
---
以下限制适用于 Azure 备份。

| **限制** | **默认** |
| --- | --- |
| 可以在保管库中注册的服务器/计算机数 | Windows Server/Windows Client/System Center DPM：50 <br/><br/> IaaS VM：1000  |
| 保管库存储中的数据源大小 |最大 54400 GB。 此限制不适用于 IaaS VM 备份 |
| Azure 订阅中的备份保管库数 |每个区域 500 个保管库 |
| 计划每日备份 |Windows Server/客户端：每天 3 次<br/> System Center DPM：每天 2 次 <br/> IaaS VM：每天 1 次  |
| 附加到 Azure VM 进行备份的数据磁盘数 | 16 |
| 附加到 Azure VM 进行备份的单个数据磁盘| 4095 GB|
