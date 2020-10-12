---
title: 将 Azure VM 的 SUSE Linux Enterprise Server 升级到 SUSE 15 SP1 | Microsoft Docs
description: 本文介绍如何使用 SUSE 分发迁移系统为 Azure 虚拟机将 SUSE Linux Enterprise server 升级到 SUSE 15 SP1 的常规步骤。
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359505"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>将 Azure VM 的 SLES 12 升级到 SLES 15 SP1

本文介绍如何为 Azure 虚拟机 (VM) 将 SUSE Linux Enterprise Server (SLES) 12 升级到 SLES 15 SP1 的常规步骤。 有关详细信息，请参阅[使用 SUSE 分发迁移系统](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html)和 [SUSE Linux Enterprise Server 15 SP1 升级指南](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update)。

## <a name="supported-upgrade-paths"></a>支持的升级路径
若要升级到 SLES 15 SP1，当前 SLES 版本必须为 SLES 12 SP4 或 12 SP5。

![有关支持的升级路径的屏幕截图](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>先决条件

- 按批准的停机时间范围规划迁移活动。 这是因为 VM 会在迁移过程中重启。
- 在进行迁移活动之前，请对 VM 执行完整备份。
- 如果未配置备份，请对 OS 磁盘进行快照备份。
- [检查 VM 是 V1 代还是 V2 代](#check-the-generation-version-for-a-vm)。

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>从 SUSE 12 SP4 或 SP5 升级到 SUSE 15 SP1

1. 为 VM 安装最新包：

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. 安装完成后，重启 VM。

3. 验证内核和操作系统版本。 请确保版本为 SUSE 12 SP4 或 SUSE 12 SP5。

    ```
    uname -a
    cat /etc/os-release
    ```

4. 安装 suse-migration-sle15-activation。 包 suse-migration-sle15-activation 安装完成后，另一个包 SLES15-Migration 将作为依赖项包自动安装 。 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. 安装完成后，请运行 `reboot` 命令以重启 VM。

6. 转到 [Azure 门户](https://portal.azure.com)，选择 VM，然后选择“串行控制台”。 系统会在“reboot: Restarting system”处停止。 此过程大约需要 15 到 45 分钟。 对于第 2 代 VM，它可能会在“reboot: Restarting system”屏幕上卡住。 在这种情况下，请等待 45 分钟。 如果之后仍无进展，请转到 Azure 门户中 VM 的“概述”页，停止 VM，然后重启 VM。

     ![有关串行控制台中的消息的屏幕截图。](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. 使用新内核重启系统后，将看到以下消息。

     ![在系统重新启动后，与新内核有关的消息的屏幕截图。](./media/linux-upgrade-suse-15sp1/output-message.png)
9. 验证内核和操作系统版本以检查系统是否已成功升级。

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>检查 VM 的代系版本

可以使用以下方法之一来检查代系版本：

- 在 SLES 终端中，运行命令 `dmidecode | grep -i hyper`。 如果是 V1 代 VM，则其不会返回任何输出。 对于 V2 代 VM，将看到以下输出：

     ![有关第 2 代 VM 输出的屏幕截图](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- 在 [Azure 门户](https://portal.azure.com)中，转到 VM 的“属性”，然后检查“VM 代系”字段 。
