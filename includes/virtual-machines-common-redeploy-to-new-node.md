---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160287"
---
## <a name="use-the-azure-portal"></a>使用 Azure 门户
1. 选择想要重新部署的 VM，然后选择“设置”边栏选项卡中的“重新部署”按钮。 可能需要向下滚动，查看包含“重新部署”按钮的“支持和故障排除”部分，如以下示例所示：
   
    ![Azure VM 边栏选项卡](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. 若要确认该操作，请选择“重新部署”按钮：
   
    ![“重新部署 VM”边栏选项卡](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. VM 准备好重新部署时，该 VM 的“状态”会更改为“正在更新”，如以下示例所示：
   
    ![VM 正在更新](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. VM 在新的 Azure 主机上启动时，“状态”将更改为“正在启动”，如以下示例所示：
   
    ![VM 正在启动](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. VM 完成启动过程后，“状态”返回到“正在运行”，这表示 VM 已成功重新部署：
   
    ![VM 正在运行](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

