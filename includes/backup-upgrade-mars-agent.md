---
title: 升级 Azure 备份代理
description: 此信息说明为什么应升级 Azure 备份代理，以及应在何处下载升级。
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
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251532"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理

低于 2.0.9083.0 的 Microsoft Azure 恢复服务 (MARS) 代理版本依赖于 Azure 访问控制服务 (ACS)。 MARS 代理也称为 Azure 备份代理。 在 2018 年，Azure [弃用了 Azure 访问控制服务 (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md)。 从 2018 年 3 月 19 日开始，低于 2.0.9083.0 的所有 MARS 代理版本会遇到备份失败。 若要避免或解决备份失败，请[将 MARS 代理升级到最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要确定需要 MARS 代理升级的服务器，请按照[用于升级 MARS 代理的备份博客](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)中的步骤操作。 MARS 代理用于将文件、文件夹和系统状态数据备份到 Azure。 System Center DPM 和 Azure 备份服务器使用 MARS 代理将数据备份到 Azure。
