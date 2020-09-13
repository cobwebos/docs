---
title: 从 Azure 自动化更新管理中删除 VM
description: 本文介绍如何删除使用“更新管理”管理的计算机。
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648630"
---
# <a name="remove-vms-from-update-management"></a>从“更新管理”中删除 VM

完成对环境中 VM 的更新管理后，可以停止通过[更新管理](update-mgmt-overview.md)功能管理 VM。 要停止管理它们，你将在 `MicrosoftDefaultComputerGroup` 链接到自动化帐户的 Log Analytics 工作区中编辑保存的搜索查询。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>删除 VM

1. 在 Azure 门户中，从 Azure 门户的顶部导航开始 **Cloud Shell** 。 如果不熟悉 Azure Cloud Shell，请参阅 [Azure Cloud Shell 概述](../../cloud-shell/overview.md)。

2. 使用以下命令识别希望从管理中删除的计算机的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Azure 门户中，导航到 **Log Analytics 工作区**。 从列表中选择你的工作区。

4. 在 Log Analytics 工作区中，选择 " **日志** "，然后从顶部的 "操作" 菜单中选择 " **查询资源管理器** "。

5. 在右侧窗格的 " **查询资源管理器** " 中，展开 " **保存的 Queries\Updates** " 并选择已保存的搜索查询 `MicrosoftDefaultComputerGroup` 进行编辑。

6. 在查询编辑器中，查看查询并查找 VM 的 UUID。 删除 VM 的 UUID，并对要删除的任何其他 Vm 重复上述步骤。

7. 通过从顶部栏中选择 " **保存** "，保存已保存的搜索。

>[!NOTE]
>取消注册后，系统仍会显示这些计算机，因为我们会报告在过去 24 小时内评估的所有计算机。 删除计算机后，需要等待24小时，然后再再列出。

## <a name="next-steps"></a>后续步骤

若要重新启用管理虚拟机，请参阅 [通过浏览 Azure 门户启用更新管理](update-mgmt-enable-portal.md) 或 [从 Azure VM 启用更新管理](update-mgmt-enable-vm.md)。