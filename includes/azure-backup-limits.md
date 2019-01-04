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
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109087"
---
以下限制适用于 Azure 备份。

| **限制** | **默认** |
| --- | --- |
| 可以在保管库中注册的服务器/计算机数 | Windows Server/Windows Client/System Center DPM：50 <br/><br/> IaaS VM：1000  |
| 保管库存储中的数据源大小 |最大 54400 GB。 此限制不适用于 IaaS VM 备份 |
| Azure 订阅中的备份保管库数 |每个区域 500 个保管库 |
| 计划每日备份 |Windows Server/客户端：每天 3 次<br/> System Center DPM：每天 2 次 <br/> IaaS VM：每天 1 次  |
| 附加到 Azure VM 进行备份的数据磁盘数 | 32 |
| 附加到 Azure VM 进行备份的单个数据磁盘| 4095 GB|



