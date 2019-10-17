---
title: 支持将 Azure Site Recovery 与 Azure 备份配合使用
description: 概述如何将 Azure Site Recovery 和 Azure 备份一起使用。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376215"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支持将 Site Recovery 与 Azure 备份配合使用

本文汇总了将[Site Recovery 服务](site-recovery-overview.md)与[Azure 备份服务](https://docs.microsoft.com/azure/backup/backup-overview)结合使用的支持。

**Action** | **Site Recovery 支持** | **详细信息**
--- | --- | ---
**一起部署服务** | 受支持 | 服务具有互操作性，可一起配置。
**文件备份/还原** | 受支持 | 为 VM 启用备份和复制并进行备份时，还原源端 Vm 或 Vm 组上的文件没有任何问题。 复制会照常继续，但不会更改复制运行状况。
**磁盘还原** | 无当前支持 | 如果还原已备份的磁盘，则需要再次禁用并重新启用 VM 复制。
**VM 还原** | 无当前支持 | 如果还原 VM 或 vm 组，则需要为 VM 禁用和重新启用复制。  


