---
title: 从 Azure 自动化更新管理中删除 VM
description: 本文介绍如何删除通过更新管理管理的计算机。
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449913"
---
# <a name="remove-vms-from-update-management"></a>从“更新管理”中删除 VM

在环境中的 Vm 上完成管理更新时，可以停止管理具有[更新管理](update-mgmt-overview.md)功能的 vm。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

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

若要重新启用管理虚拟机，请参阅[通过浏览 Azure 门户启用更新管理](update-mgmt-enable-portal.md)或[从 Azure VM 启用更新管理](update-mgmt-enable-vm.md)。