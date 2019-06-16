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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238614"
---
以下限制适用于 Azure 备份。

| **限制** | **默认** |
| --- | --- |
| 服务器或可以在保管库中注册的计算机。 | Windows Server/Windows 客户端/System Center Data Protection Manager:50. <br/><br/> IaaS VM：1,000.  |
| 保管库存储中的数据源的大小。 |54,400 GB 最大值。 此限制不适用于 IaaS VM 备份。 |
| Azure 订阅中的备份保管库。 |每个区域 500 个保管库。 |
| 计划每日备份。 |Windows Server/客户端：每天三次。<br/> System Center DPM：两个一天。 <br/> IaaS VM：每天一次。  |
| 数据磁盘附加到 Azure VM 备份。 | 16 |
| 单个数据磁盘附加到 Azure VM 备份。| 4,095 GB|
