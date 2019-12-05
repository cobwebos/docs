---
title: include 文件
description: include 文件
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828997"
---
以下限制适用于 Azure 备份。

| **限制** | **默认** |
| --- | --- |
| 可在保管库中注册的服务器或计算机。 | Windows Server/Windows 客户端/System Center Data Protection Manager：50。 <br/><br/> IaaS Vm：1000。  |
| 保管库存储中的数据源大小。 |最大 54400 GB。 此限制不适用于 IaaS VM 备份。 |
| Azure 订阅中的备份保管库。 |每个区域500个保管库。 |
| 计划每日备份。 |Windows Server/客户端：每天三次。<br/> System Center DPM：2天。 <br/> IaaS Vm：一天一次。  |
| 附加到用于备份的 Azure VM 的数据磁盘。 | 16 |
| 附加到 Azure VM 以进行备份的单个数据磁盘。| 32 TB|
