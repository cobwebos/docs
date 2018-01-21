---
title: "Azure 备份：Linux VM 的应用程序一致性备份 | Microsoft Docs"
description: "创建 Linux 虚拟机到 Azure 的应用程序一致性备份。 本文介绍如何配置脚本框架以备份 Azure 部署的 Linux VM。 本文还包括故障排除信息。"
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "应用程序一致性备份; 应用程序一致性 Azure VM 备份; Linux VM 备份; Azure 备份"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/12/2018
ms.author: anuragm;markgal
ms.openlocfilehash: c2437b4cd90deda3e7239d87837a47a072f52835
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2018
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure Linux VM 的应用程序一致性备份

创建 VM 的备份快照时，应用程序一致性意味着 VM 在还原后启动时启动应用程序。 正如你所想象，应用程序一致性非常重要。 若要确保 Linux VM 是应用程序一致的，可以使用 Linux 前脚本和后脚本框架执行应用程序一致性备份。 前脚本和后脚本框架支持 Azure 资源管理器部署的 Linux 虚拟机。 应用程序一致性脚本不支持 Service Manager 部署的虚拟机或 Windows 虚拟机。

## <a name="how-the-framework-works"></a>框架工作原理

该框架提供一个选项，用于在创建 VM 快照时运行自定义的操作前脚本和操作后脚本。 前脚本在创建 VM 快照前的那一刻运行，后脚本在创建 VM 快照后立即运行。 使用前脚本和后脚本可在创建 VM 快照时灵活控制应用程序和环境。

前脚本可以调用本机应用程序 API 来使 IO 处于静默状态并将内存中内容刷新到磁盘。 这些操作可确保快照是应用程序一致的。 后脚本使用本机应用程序 API 来解冻 IO，使应用程序能够在创建 VM 快照后恢复正常操作。

## <a name="steps-to-configure-pre-script-and-post-script"></a>配置操作前脚本和操作后脚本的步骤

1. 以 root 用户身份登录到要备份的 Linux VM。

2. 从 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下载 **VMSnapshotScriptPluginConfig.json**，并将其复制到要备份的所有 VM 的 **/etc/azure** 文件夹。 如果 **/etc/azure** 文件夹不存在，则创建它。

3. 将应用程序的前脚本和后脚本复制到计划备份的所有 VM 上。 可将脚本复制到 VM 上的任意位置。 确保在 VMSnapshotScriptPluginConfig.json 文件中更新脚本文件的完整路径。

4. 确保分配对这些文件的以下权限：

   - VMSnapshotScriptPluginConfig.json：权限“600”。 例如，只有“root”用户才对此文件拥有“读取”和“写入”权限，任何用户都不得拥有其“执行”权限。

   - 操作前脚本文件：权限“700”。  例如，只有“root”用户才对此文件拥有“读取”、“写入”和“执行”权限。
  
   - 操作后脚本：权限“700”。 例如，只有“root”用户才对此文件拥有“读取”、“写入”和“执行”权限。

   > [!Important]
   > 该框架为用户提供强大功能。 保护该框架，并确保只有“root”用户可以访问关键 JSON 和脚本文件。
   > 如果不符合要求，该脚本将不会运行，从而导致文件系统崩溃和生成不一致性备份。
   >

5. 根据下述信息配置 VMSnapshotScriptPluginConfig.json：
    - **pluginName**：将此字段保留原样，否则脚本可能无法按预期工作。

    - preScriptLocation：提供要备份的 VM 上的操作前脚本的完整路径。

    - postScriptLocation：提供要备份的 VM 上的操作后脚本的完整路径。

    - preScriptParams：提供需传递给操作前脚本的可选参数。 所有参数都应当用引号引起来。 如果使用多个参数，请用逗号分隔参数。

    - postScriptParams：提供需传递给操作后脚本的可选参数。 所有参数都应当用引号引起来。 如果使用多个参数，请用逗号分隔参数。

    - preScriptNoOfRetries：设置发生任何错误时，程序终止前应重试操作前脚本的次数。 0 表示发生失败时只尝试一次且不重试。

    - postScriptNoOfRetries：设置发生任何错误时，程序终止前应重试操作后脚本的次数。 0 表示发生失败时只尝试一次且不重试。
    
    - timeoutInSeconds：指定操作前脚本和操作后脚本的单次超时。

    - continueBackupOnFailure：如果希望在操作前脚本或操作后脚本失败时 Azure 备份故障回复到文件系统/崩溃一致性备份，请将此值设置为 true。 如果将此值设置为 false，则脚本失败时，备份也会失败（除非拥有单磁盘 VM，它会故障回复到崩溃一致性备份，不管此项设置如何）。

    - fsFreezeEnabled：指定为了确保文件系统一致性，在创建 VM 快照时是否应调用 Linux fsfreeze。 建议将此设置保留为 true，除非必须禁用 fsfreeze 才能让应用程序正常工作。

6. 现已配置脚本框架。 如果已配置 VM 备份，则下一次备份将调用这些脚本，并触发应用程序一致性备份。 如果未配置 VM 备份，请使用[将 Azure 虚拟机备份到恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)进行配置。

## <a name="troubleshooting"></a>故障排除

编写操作前脚本和操作后脚本时，确保添加相应的日志记录，并查看脚本日志来解决任何脚本问题。 如果运行脚本时仍然遇到问题，请参阅下表中的详细信息。

| 错误 | 错误消息 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |操作前脚本返回错误，因此备份可能不具有应用程序一致性。   | 查看脚本的失败日志来解决问题。|  
|   Post-ScriptExecutionFailed |    操作后脚本返回了可能影响应用程序状态的错误。 |    查看脚本的失败日志来解决问题，并检查应用程序状态。 |
| Pre-ScriptNotFound |  在 VMSnapshotScriptPluginConfig.json 配置文件中指定的位置找不到操作前脚本。 |   确保配置文件中指定的路径处存在操作前脚本，以保证应用程序一致性备份。|
| Post-ScriptNotFound | 在 VMSnapshotScriptPluginConfig.json 配置文件中指定的位置找不到操作后脚本。 |   确保配置文件中指定的路径处存在操作后脚本，以保证应用程序一致性备份。|
| IncorrectPluginhostFile | VmSnapshotLinux 扩展随附的 Pluginhost 文件已损坏，因此操作前脚本和操作后脚本无法运行，且不会创建应用程序一致性的备份。 | 卸载 VmSnapshotLinux 扩展，下一次备份时会自动重新安装它，这样即可解决问题。 |
| IncorrectJSONConfigFile | VMSnapshotScriptPluginConfig.json 文件不正确，因此操作前脚本和操作后脚本无法运行，且不会创建应用程序一致的备份。 | 从 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下载副本并重新配置该文件。 |
| InsufficientPermissionforPre-Script | 对于正在运行的脚本，“root”用户应是该文件的所有者，并且应对文件设置“700”权限（即只有“所有者”才拥有“读取”、“写入”和“执行”权限）。 | 确保“root”用户是脚本文件的“所有者”，只有“所有者”才拥有“读取”、“写入”和“执行”权限。 |
| InsufficientPermissionforPost-Script | 对于正在运行的脚本，root 用户应是该文件的所有者，并且应该对文件设置“700”权限（即，只有“所有者”才拥有“读取”、“写入”和“执行”权限）。 | 确保“root”用户是脚本文件的“所有者”，只有“所有者”才拥有“读取”、“写入”和“执行”权限。 |
| Pre-ScriptTimeout | 执行应用程序一致性备份时操作前脚本超时。 | 检查脚本，并在 /etc/azure 中的 VMSnapshotScriptPluginConfig.json 文件中增加超时时间。 |
| Post-ScriptTimeout | 执行应用程序一致性备份时操作后脚本超时。 | 检查脚本，并在 /etc/azure 中的 VMSnapshotScriptPluginConfig.json 文件中增加超时时间。 |

## <a name="next-steps"></a>后续步骤
[配置 VM 到恢复服务保管库的备份](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
