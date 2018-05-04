---
title: 排查 System Center Data Protection Manager 和 Azure 备份问题 | Microsoft Docs
description: 排查 System Center Data Protection Manager 中的问题。
services: backup
documentationcenter: ''
author: adigan
manager: shreeshd
editor: ''
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 8f8117b216dcbda217bc433e643090e8eb47cf57
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>排查 System Center Data Protection Manager 问题

本文介绍在使用 Data Protection Manager 时可能遇到的问题的解决方法。

有关 System Center Data Protection Manager 的最新发行说明，请参阅 [System Center 文档](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)。 可在[此矩阵](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)中详细了解 Data Protection Manager 支持的功能。


## <a name="error-replica-is-inconsistent"></a>错误：副本不一致

副本可能出于以下原因而不一致：
- 副本创建作业失败。
- 更改日记存在问题。
- 卷级筛选器位图包含错误。
- 源计算机意外关闭。
- 同步日志溢出。
- 副本确实不一致。

若要解决此问题，请执行以下操作：
- 若要消除不一致状态，请手动运行一致性检查，或计划每日一致性检查。
- 确保使用最新版本的 Microsoft Azure 备份服务器和 Data Protection Manager。
- 确保启用“自动一致性”设置。
- 尝试从命令提示符重启服务。 使用 `net stop dpmra` 命令，后接 `net start dpmra`。
- 确保符合网络连接和带宽要求。
- 检查源计算机是否意外关闭。
- 确保磁盘正常，并且有足够的空间用于副本。
- 确保没有重复的备份作业在同时运行。

## <a name="error-online-recovery-point-creation-failed"></a>错误：在线恢复点创建失败

若要解决此问题，请执行以下操作：
- 确保使用最新版本的 Azure 备份代理。
- 尝试在保护任务区域中手动创建恢复点。
- 确保对数据源运行一致性检查。
- 确保符合网络连接和带宽要求。
- 当副本数据处于不一致状态时，请创建此数据源的磁盘恢复点。
- 确保副本存在而未缺失。
- 确保副本有足够的空间用于创建更新序列号 (USN) 日记。

## <a name="error-unable-to-configure-protection"></a>错误：无法配置保护

如果 Data Protection Manager 服务器无法访问受保护的服务器，则会出现此错误。 

若要解决此问题，请执行以下操作：
- 确保使用最新版本的 Azure 备份代理。
- 确保在 Data Protection Manager 服务器与受保护服务器之间建立连接（网络/防火墙/代理）。
- 若要保护 SQL 服务器，请确保“登录属性” > “NT AUTHORITY\SYSTEM”属性显示已启用“sysadmin”设置。

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>错误：未根据保管库凭据文件中的指定注册服务器

此错误是在恢复 Data Protection Manager/Azure 备份服务器数据的过程中发生的。 在恢复过程中使用的保管库凭据文件不属于 Data Protection Manager/Azure 备份服务器的恢复服务保管库。

若要解决此问题，请执行以下步骤：
1. 从 Data Protection Manager/Azure 备份服务器所注册到的恢复服务保管库中下载保管库凭据文件。
2. 尝试使用下载的最新保管库凭据文件将服务器注册到保管库。

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>错误：没有可恢复的数据，或选定的服务器不是 Data Protection Manager 服务器

此错误的原因如下：
- 未将其他 Data Protection Manager/Azure 备份服务器注册到恢复服务保管库。
- 服务器尚未上传元数据。
- 选定的服务器不是 Data Protection Manager/Azure 备份服务器。

如果已将其他 Data Protection Manager/Azure 备份服务器注册到恢复服务保管库，请执行以下步骤来解决此问题：
1. 确保安装最新的 Azure 备份代理。
2. 确保安装最新的代理后，先等待一天，然后启动恢复过程。 夜间备份作业会将所有受保护的备份的元数据上传到云。 然后，可以使用备份数据进行恢复。

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>错误：提供的加密通行短语与服务器的通行短语不匹配

此错误是在加密 Data Protection Manager/Azure 备份服务器数据的过程中发生的。 恢复过程中使用的加密通行短语与服务器的加密通行短语不匹配。 因此，代理无法解密数据，恢复失败。

> [!IMPORTANT]
> 如果忘记或遗失了加密通行短语，则没有其他任何方法可恢复数据。 唯一的办法就是重新生成通行短语。 使用新的通行短语来加密今后的备份数据。
>
> 恢复数据时，请始终提供与 Data Protection Manager/Azure 备份服务器关联的相同加密通行短语。 
>
