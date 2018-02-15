---
title: "排查 System Center Data Protection Manager 和 Azure 备份问题 | Microsoft Docs"
description: "排查 System Center Data Protection Manager 中的问题。"
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>排查 System Center Data Protection Manager 问题

可在[此处](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)找到 SC DPM 的最新发行说明。
可在[此处](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)找到保护支持矩阵。

## <a name="replica-is-inconsistent"></a>副本不一致

此问题可能是由各种原因造成的，例如 - 副本创建作业失败、更改日记出现问题、卷级筛选器位图错误、源计算机意外关闭、同步日志溢出，或副本其实不一致。 遵循以下步骤来解决此问题：
- 若要消除不一致状态，请手动运行一致性检查，或计划每日一致性检查。
- 确保使用最新版本的 MAB Server 或 System Center DPM
- 确保已启用自动一致性检查
- 尝试从命令提示符重启服务（依次运行“net stop dpmra”和“net stop dpmra”）
- 确保符合网络连接和带宽要求
- 检查源计算机是否意外关闭
- 确保磁盘正常，并且为副本留出了足够的空间
- 确保没有重复的备份作业在同时运行

## <a name="online-recovery-point-creation-failed"></a>在线恢复点创建失败

遵循以下步骤来解决此问题：
- 确保使用最新版本的 Azure 备份代理
- 尝试在保护任务区域中手动创建恢复点
- 确保对数据源运行一致性检查
- 确保符合网络连接和带宽要求
- 副本数据处于不一致状态。 创建此数据源的磁盘恢复点
- 确保副本存在而未缺失
- 副本中有足够的空间用于创建 USN 日记

## <a name="unable-to-configure-protection"></a>无法配置保护

当 DPM 服务器无法与受保护的服务器通信时，会出现此错误。 遵循以下步骤来解决此问题：
- 确保使用最新版本的 Azure 备份代理
- 确保在 DPM 服务器与受保护服务器之间建立连接（网络/防火墙/代理）
- 如果正在保护 SQL Server，请确保通过登录属性为 NT AUTHORITY\SYSTEM 启用 sysadmin

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>此服务器未注册到保管库凭据所指定的保管库

当所选保管库凭据文件不属于与 System Center DPM/Azure 备份服务器（在其上进行恢复尝试）关联的恢复服务保管库时，会出现此错误。 遵循以下步骤来解决此问题：
- 从 System Center DPM/Azure 备份服务器所注册到的恢复服务保管库中下载保管库凭据文件。
- 尝试使用下载的最新保管库凭据文件将服务器注册到保管库。

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>可恢复的数据不可用，或所选服务器不是 DPM 服务器
如果没有其他 System Center DPM/Azure 备份服务器注册到恢复服务保管库、服务器尚未上传元数据，或者所选服务器不是 System Center DPM/Azure 备份服务器，则会出现此错误。
- 如果有其他 System Center DPM/Azure 备份服务器注册到了恢复服务保管库，请确保安装了最新 Azure 备份代理。
- 如果有其他 System Center DPM/Azure 备份服务器注册到了恢复服务保管库，请等到安装之后的某一天来启动恢复过程。 每夜执行的作业会将所有受保护的备份的元数据上传到云。 该数据可用于恢复。

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>提供的加密密码与以下服务器的关联通行短语不匹配

> [!NOTE]
>如果忘记/丢失了加密通行短语，没有任何选项可恢复数据。 唯一的解决方法是重新生成通行短语，并使用它来加密以后的备份数据。
>
>

如果用于 System Center DPM/Azure 备份服务器的恢复数据加密过程的加密通行短语与所提供的加密通行短语不匹配，则会出现此错误。 代理不能对数据进行解密。 因此恢复失败。 遵循以下步骤来解决此问题：
- 确保提供的加密通行短语与要进行数据恢复的 System Center DPM/Azure 备份服务器的关联通行短语相同。 
