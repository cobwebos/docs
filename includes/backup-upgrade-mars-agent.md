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
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197108"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理

2\.0.9083.0 下的 Microsoft Azure 恢复服务（MARS）代理的版本依赖于 Azure 访问控制服务。 MARS 代理也称为 Azure 备份代理。

在2018中，Microsoft 不[推荐使用 Azure 访问控制服务](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 从2018年3月19日开始，2.0.9083.0 以下 MARS 代理的所有版本都将遇到备份失败。 若要避免或解决备份失败，请[将 MARS 代理升级到最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要确定需要 MARS 代理升级的服务器，请按照备份博客中的步骤[升级 MARS 代理](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)。

MARS 代理用于将文件和文件夹以及系统状态数据备份到 Azure。 System Center DPM 和 Azure 备份服务器使用 MARS 代理将数据备份到 Azure。
