---
title: 升级 Azure 备份代理
description: 此信息说明了为何应升级 Azure 备份代理，以及下载升级的位置。
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673199"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理

2\.0.9083.0 下的 Microsoft Azure 恢复服务（MARS）代理的版本依赖于 Azure 访问控制服务。 MARS 代理也称为 Azure 备份代理。

在2018中，Microsoft 不[推荐使用 Azure 访问控制服务](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 从2018年3月19日开始，2.0.9083.0 以下 MARS 代理的所有版本都将遇到备份失败。 若要避免或解决备份失败，请[将 MARS 代理升级到最新版本](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)。 若要确定需要 MARS 代理升级的服务器，请按照[升级 Microsoft Azure 恢复服务（MARS）代理](../articles/backup/upgrade-mars-agent.md)中的步骤操作。

MARS 代理用于将文件和文件夹以及系统状态数据备份到 Azure。 System Center DPM 和 Azure 备份服务器使用 MARS 代理将数据备份到 Azure。
