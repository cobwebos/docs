---
title: 从 Azure 自动化更新管理中删除 VM
description: 本文介绍如何删除通过更新管理管理的计算机。
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610041"
---
# <a name="remove-vms-from-update-management"></a>从“更新管理”中删除 VM

向环境中的 VM 部署更新完成后，可以从[更新管理](automation-update-management.md)功能中将 VM 删除。

## <a name="to-remove-your-vms"></a>删除 Vm

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 使用以下命令识别要从管理中删除的计算机的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 "**常规**" 下的 "Log Analytics" 工作区中，访问作用域配置的已保存搜索 `MicrosoftDefaultScopeConfig-Updates` 。

4. 对于保存的搜索 `MicrosoftDefaultComputerGroup`，单击右侧的省略号，然后选择“编辑”。

5. 删除 VM 的 UUID。

6. 对要删除的任何其他 VM 重复上述步骤。

7. 编辑完保存的搜索后，再次保存。

>[!NOTE]
>由于在最近24小时内评估的所有计算机进行了取消注册，因此在你对其进行了后，仍会显示计算机。 断开计算机的连接后，需要等待24小时，然后再再列出。

## <a name="next-steps"></a>后续步骤

* 若要继续使用更新管理，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要解决一般的功能问题，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 有关 Windows 更新代理的问题，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 有关 Linux 更新代理的问题，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。