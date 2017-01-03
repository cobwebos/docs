---
title: "Azure 备份限制表"
description: "描述 Azure 备份的系统限制。"
services: backup
documentationcenter: NA
author: Jim-Parker
manager: jwhit
editor: 
ms.service: backup
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/05/2015
ms.author: trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 4b234af8249a64e8d56e9f4c41815749f154af3e
ms.openlocfilehash: e7147f7712fd22fc07d7af551a12a07808bca9b9


---
以下限制适用于 Azure 备份。

| 限制标识符 | 默认限制 |
| --- | --- |
| 可针对每个保管库注册的服务器/计算机数量 |对于 Windows Server/客户端/SCDPM 为 50 个 <br/> 对于 IaaS VM 为 200 个 |
| 存储在 Azure 保管库存储中的数据的数据源大小 |最大 54400 GB<sup>1</sup> |
| 可在每个 Azure 订阅中创建的备份保管库的数目 |25(Backup vaults) <br/> 每个区域 25 个恢复服务保管库 |
| 每天计划备份的次数 |对于 Windows Server/客户端为每天 3 个 <br/> 对于 SCDPM 为每天 2 个 <br/> 对于 IaaS Vm 为每天 1 个 |
| 将数据磁盘附加到 Azure 虚拟机进行备份 |16 |

* <sup>1</sup>54400 GB 限制不适用于 IaaS VM 备份。




<!--HONumber=Dec16_HO3-->


