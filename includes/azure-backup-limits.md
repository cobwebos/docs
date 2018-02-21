---
title: "include 文件"
description: "include 文件"
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk
ms.custom: include file
ms.openlocfilehash: 7ca5b34961b4d0e3d4fcecb8175e3e0901d7049d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
以下限制适用于 Azure 备份。

| 限制标识符 | 默认限制 |
| --- | --- |
| 可针对每个保管库注册的服务器/计算机数量 |对于 Windows Server/客户端/SCDPM 为 50 个 <br/> 对于 IaaS VM 为 200 个 |
| 存储在 Azure 保管库存储中的数据的数据源大小 |最大 54400 GB<sup>1</sup> |
| 可在每个 Azure 订阅中创建的备份保管库的数目 |每个区域 25 个恢复服务保管库 |
| 每天计划备份的次数 |对于 Windows Server/客户端为每天 3 个 <br/> 对于 SCDPM 为每天 2 个 <br/> 对于 IaaS Vm 为每天 1 个 |
| 将数据磁盘附加到 Azure 虚拟机进行备份 |16 |
| 附加到 Azure 虚拟机以进行备份的单个数据磁盘的大小| 1024 GB <sup>2</sup>|

* <sup>1</sup>54400 GB 限制不适用于 IaaS VM 备份。
* <sup>2</sup> 我们提供了[个人预览版](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0)，最多可支持 4TB 的磁盘。 

