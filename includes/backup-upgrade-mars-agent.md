---
title: 升级 Azure 备份代理
description: 此信息说明为什么应升级 Azure 备份代理，以及应在何处下载升级。
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750324"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理
低于 2.0.9083.0 的 Microsoft Azure 恢复服务 (MARS) 代理版本依赖于 Azure 访问控制服务 (ACS)。 在 2018 年，Azure 将[弃用 Azure 访问控制服务 (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md)。 从 2018 年 3 月 19 日开始，低于 2.0.9083.0 的所有 MARS 代理版本会遇到备份失败。 若要避免或解决备份失败，请[将 MARS 代理升级到最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要确定需要 MARS 代理升级的服务器，请按照[用于升级 Azure 备份代理的备份博客](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)中的步骤操作。 MARS 代理用于将以下数据类型备份到 Azure：
- 文件 
- 系统状态数据
- 将 System Center DPM 备份到 Azure
- Azure 备份服务器 (MABS)
