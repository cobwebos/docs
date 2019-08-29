---
title: 支持将 Azure Site Recovery 与 Azure 备份配合使用
description: 概述如何将 Azure Site Recovery 和 Azure 备份一起使用。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146872"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支持将 Site Recovery 与 Azure 备份配合使用

本文汇总了将[Site Recovery 服务](site-recovery-overview.md)与[Azure 备份服务](https://docs.microsoft.com/azure/backup/backup-overview)结合使用的支持。

**Action** | **Site Recovery 支持** | **详细信息**
--- | --- | ---
**一起部署服务** | 支持 | 服务具有互操作性, 可一起配置。
**文件备份/还原** | 支持 | 为 VM 启用备份和复制并进行备份时, 还原源端 Vm 或 Vm 组上的文件没有任何问题。 复制会照常继续, 但不会更改复制运行状况。
**磁盘备份/还原** | 无当前支持 | 如果还原已备份的磁盘, 则需要再次禁用和重新启用 VM 复制。
**VM 备份/还原** | 无当前支持 | 如果备份或还原 VM 或 vm 组, 则需要禁用和重新启用 VM 复制。  


