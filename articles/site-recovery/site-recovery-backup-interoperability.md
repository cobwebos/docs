---
title: 使用 Azure Site Recovery 与 Azure 备份的支持 |Microsoft Docs
description: 概述如何 Azure Site Recovery 和 Azure 备份可以一起使用。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035725"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>使用 Site Recovery 与 Azure 备份的支持

本文汇总了支持使用[Site Recovery 服务](site-recovery-overview.md)连同[Azure 备份服务](https://docs.microsoft.com/azure/backup/backup-overview)。

**Action** | **Site Recovery 支持** | **详细信息**
--- | --- | ---
**将服务部署在一起** | 支持 | 服务是可互操作，可以配置在一起。
**文件备份/还原** | 支持 | 如果为 VM 启用备份和复制备份，没有问题中还原源端虚拟机或组的 Vm 上的文件。 复制将像往常一样继续复制运行状况中进行任何更改。
**磁盘备份/还原** | 没有当前支持 | 如果要还原备份的磁盘，需要禁用和重新启用再次复制虚拟机。
**VM 备份/还原** | 没有当前支持 | 如果备份或还原的 VM 或 Vm 的组，您需要禁用和重新启用 VM 的复制。  


