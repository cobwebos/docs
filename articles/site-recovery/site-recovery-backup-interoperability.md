---
title: 支持将 Azure Site Recovery 与 Azure 备份配合使用
description: 概述如何将 Azure Site Recovery 与 Azure 备份一起使用。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135171"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>支持将 Site Recovery 与 Azure 备份配合使用

本文总结了对将 [Site Recovery 服务](site-recovery-overview.md)与 [Azure 备份服务](../backup/backup-overview.md)一起使用的支持。

**Action** | **Site Recovery 支持** | **详细信息**
--- | --- | ---
**一起部署服务** | 支持 | 服务具有互操作性，可一起配置。
**文件备份/还原** | 支持 | 为 VM 启用备份和复制并进行备份时，在源端 VM 或 VM 组上还原文件不会出现问题。 复制照常继续，复制运行状况没有任何变化。
磁盘还原 | 目前不支持 | 如果还原已备份的磁盘，则需要再次禁用并重新启用 VM 的复制。
**VM 还原** | 目前不支持 | 如果还原一个 VM 或一组 VM，则需要禁用并重新启用 VM 的复制。  


