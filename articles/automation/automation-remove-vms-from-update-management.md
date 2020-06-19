---
title: 从 Azure 自动化更新管理中删除 VM
description: 本文介绍如何从“更新管理”中删除 VM。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836645"
---
# <a name="remove-vms-from-update-management"></a>从“更新管理”中删除 VM

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

向环境中的 VM 部署更新完成后，可以从[更新管理](automation-update-management.md)功能中将 VM 删除。

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 使用以下命令识别希望从管理中删除的 VM 的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Log Analytics 工作区的“常规”下，访问保存的搜索。

4. 对于保存的搜索 `MicrosoftDefaultComputerGroup`，单击右侧的省略号，然后选择“编辑”。 

5. 删除 VM 的 UUID。

6. 对要删除的任何其他 VM 重复上述步骤。

7. 编辑完保存的搜索后，再次保存。 

## <a name="next-steps"></a>后续步骤

* 若要继续使用更新管理，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要解决一般的功能问题，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 有关 Windows 更新代理的问题，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 有关 Linux 更新代理的问题，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。