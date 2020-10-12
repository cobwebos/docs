---
title: 在 Azure 开发测试实验室的实验室中重新部署 VM | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中重新部署虚拟机（从一个 Azure 节点移动到另一个 Azure 节点）。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a38b112165b893d877733b967c21bb62b20ca2f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530312"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中重新部署 VM
如果无法通过远程桌面连接连接到实验室中 (VM) ，请重新部署 VM，并再次尝试连接到该虚拟机。 重新部署 VM 时，开发测试实验室会将 VM 从在其上运行该 VM 的节点移动到 Azure 基础结构中的新节点。 然后启动 VM，同时保留所有配置选项和关联资源。 此功能可为你节省排查远程桌面连接问题或在实验室中通过应用程序访问基于 Windows 的 VM 所用的时间。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>在实验室中重新部署 VM 的步骤 
若要在 Azure 开发测试实验室的实验室中重新部署 VM，请执行以下步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”****，并从列表中选择“开发测试实验室”****。
3. 从实验室列表中，选择包含要重新部署 VM 的实验室。  
4. 在左面板中，选择“我的虚拟机”****。 
5. 从 VM 列表中选择 VM。
6. 在 VM 虚拟机页上，选择左侧菜单“操作”**** 下的“重新部署”****。

    ![屏幕捕获显示选择了重新部署的虚拟机页面。](media/devtest-lab-redeploy-vm/redeploy.png)
7. 阅读页面上的信息，然后选择“重新部署”**** 按钮。 9. 检查“通知”**** 窗口中重新部署操作的状态。

    ![重新部署状态](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>后续步骤
若要了解如何在 Azure 开发测试实验室中调整 VM 大小，请参阅[调整 VM 大小](devtest-lab-resize-vm.md)。


