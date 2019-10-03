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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67173350"
---
以下限制适用于 Azure 备份。

| **限制** | **默认** |
| --- | --- |
| 可在保管库中注册的服务器或计算机。 | Windows Server/Windows 客户端/System Center Data Protection Manager：50. <br/><br/> IaaS VM：1000。  |
| 保管库存储中的数据源大小。 |最大 54400 GB。 此限制不适用于 IaaS VM 备份。 |
| Azure 订阅中的备份保管库。 |每个区域500个保管库。 |
| 计划每日备份。 |Windows Server/客户端：3天。<br/> System Center DPM：2天。 <br/> IaaS VM：每天一次。  |
| 附加到用于备份的 Azure VM 的数据磁盘。 | 16 |
| 附加到 Azure VM 以进行备份的单个数据磁盘。| 4,095 GB|
