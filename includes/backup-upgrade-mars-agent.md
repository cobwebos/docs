---
title: 升级 Azure 备份代理
description: 此信息解释了为什么应升级 Azure 备份代理，以及下载升级的位置。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673199"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理

低于 2.0.9083.0 的 Microsoft Azure 恢复服务 （MARS） 代理版本依赖于 Azure 访问控制服务。 MARS 代理也称为 Azure 备份代理。

2018 年，微软[弃用 Azure 访问控制服务](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 从 2018 年 3 月 19 日开始，低于 2.0.9083.0 的所有版本的 MARS 代理都将遇到备份故障。 为了避免或解决备份故障，[请将 MARS 代理升级到最新版本](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)。 要标识需要 MARS 代理升级的服务器，请按照[升级 Microsoft Azure 恢复服务 （MARS） 代理](../articles/backup/upgrade-mars-agent.md)中的步骤操作。

MARS 代理用于将文件和文件夹以及系统状态数据备份到 Azure。 系统中心 DPM 和 Azure 备份服务器使用 MARS 代理将数据备份到 Azure。
