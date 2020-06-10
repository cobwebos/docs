---
title: 从 Azure 自动化更改跟踪和库存中删除 VM
description: 本文介绍如何从更改跟踪和库存中删除 VM。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836662"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>从“更改跟踪和清单”中删除 VM

向环境中的 VM 部署更改完成后，可以从[更改跟踪和库存](change-tracking.md)功能中将它们删除。

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪”或“库存”  。

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

* 若要继续使用更改跟踪和库存功能，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 若要解决一般的功能问题，请参阅[排查更改跟踪和库存问题故障](troubleshoot/change-tracking.md)。